# Знакомство с Hibernate

Подключите к проекту библиотеку Hibernate

- hibernate-core

- postgresql

- hibernate-entitymanager

Создаем файл конфигурации `hibernate.cfg.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration SYSTEM "http://hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <property name="connetcion.driver_class">org.postgresql.Driver</property>
        <property name="connection.url">jdbc:postgresql://localhost:5432/test</property>
        <property name="connection.username">postgres</property>
        <property name="connection.password">123</property>
        <property name="dialect">org.hibernate.dialect.PostgreSQL95Dialect</property>
        <property name="default_schema">public</property>
        <property name="swow_sql">true</property>
        <property name="hbm2ddl.auto">create</property>
        <property name="format_sql">true</property>
        <!--<property name="hbm2ddl.auto">validate</property>-->
        <!--<property name="hbm2ddl.auto">update</property>-->

        <mapping class="io.github.personal_finance.domain.User"/>
        <mapping class="io.github.personal_finance.domain.Category"/>
        <mapping class="io.github.personal_finance.domain.Expence"/>

    </session-factory>
</hibernate-configuration>

```

Затем необходимо реализовать класс подклбчения к БД.

```java
package autobase.utils;

import org.hibernate.SessionFactory;
import org.hibernate.boot.registry.StandardServiceRegistryBuilder;
import org.hibernate.cfg.Configuration;

public class HibernateUtil {
    private static SessionFactory sessionFactory = null;

    static {
        Configuration cfg = new Configuration().configure("hibernate.cfg.xml");
        sessionFactory = cfg.buildSessionFactory();
    }

    public static SessionFactory getSessionFactory() {
        return sessionFactory;
    }
}
```

Необходимо описать все классы моделей и соотвествуюшие типы моделей в базе данных и в Java.

![image](https://github.com/rmuhamedgaliev-mkdev/personal-finance-quest/blob/master/er-diagram.png?raw=true)

Связать сущности необходимо в двусторонней связи как в [примере](http://fruzenshtein.com/bidirectional-many-to-one-association/).

```java
import javax.persistence.*;
import javax.validation.constraints.NotNull;

@Entity
@Table(name = "users")
public class User {

    @Id
    @SequenceGenerator(name = "users_seq", sequenceName = "users_seq", allocationSize = 1)
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "users_seq")
    private long id;

    @NotNull(message = "name of user can't be empty")
    @Column(name = "name", length = 256)
    private String name;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "expence_id")
    private Expence expence;

    public User() {
    }

    public User(String name) {
        this.name = name;
    }

    public long getId() {
        return id;
    }


    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setExpence(Expence expence) {
        this.expence = expence;
    }
}

```

```java
package io.github.personal_finance.domain;

import javax.persistence.*;
import java.util.List;

@Entity
@Table(name = "expence")
public class Expence {
    @Id
    @SequenceGenerator(name = "expence_seq", sequenceName = "expence_seq", allocationSize = 1)
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "expence_seq")
    private long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "category_id")
    private Category category;

    @OneToMany(fetch = FetchType.LAZY, mappedBy = "expence")
    @Column(name = "user_id")
    private List<User> users;

    public Expence(Category category) {
        this.category = category;
    }

    public long getId() {
        return id;
    }

    public Category getCategory() {
        return category;
    }

    public void setCategory(Category category) {
        this.category = category;
    }

    public List<User> getUsers() {
        return users;
    }

    public void setUsers(List<User> users) {
        this.users = users;
    }
}
```

```java
package io.github.personal_finance.domain;

import javax.persistence.*;
import javax.validation.constraints.NotNull;
import java.util.List;

@Entity
@Table(name = "category")
public class Category {
    @Id
    @SequenceGenerator(name = "category_seq", sequenceName = "category_seq", allocationSize = 1)
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "category_seq")
    private long id;

    @NotNull(message = "name of user can't be empty")
    @Column(name = "name", length = 256)
    private String name;

    @OneToMany(fetch = FetchType.LAZY, mappedBy = "category")
    @Column(name = "expence_id")
    private List<Expence> expences;

    public Category() {
    }

    public Category(String name) {
        this.name = name;
    }

    public long getId() {
        return id;
    }


    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public List<Expence> getExpences() {
        return expences;
    }

    public void setExpences(List<Expence> expences) {
        this.expences = expences;
    }
}

```

Пример отправки SQL запросов в БД:

```java
package io.github.personal_finance;

/*
 * This Java source file was generated by the Gradle 'init' task.
 */


import io.github.personal_finance.domain.Category;
import io.github.personal_finance.domain.Expence;
import io.github.personal_finance.domain.User;
import io.github.personal_finance.utils.HibernateUtils;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;

import javax.persistence.Query;

//@SpringBootConfiguration
//@SpringBootApplication(scanBasePackages = "io.github.personal_finance")
public class App {

    public static void main(String[] args) {

//        SpringApplication.run(App.class, args);

        SessionFactory sessionFactory = HibernateUtils.getSessionFactory();

        Session session = sessionFactory.openSession();
        Transaction tx = session.beginTransaction();

        User user = new User("name");
        session.save(user);

        String queryString = "FROM User u WHERE u.name =\'name\'";
        Query query = session.createQuery(queryString);
        user = (User) query.getSingleResult();

        Category category1 = new Category("category1");
        session.save(category1);

        String queryStringCategory1 = "FROM Category c WHERE c.name =\'category1\'";
        Query queryCategory1 = session.createQuery(queryStringCategory1);
        category1 = (Category) queryCategory1.getSingleResult();


        Category category2 = new Category("category2");
        session.save(category2);

        String queryStringCategory2 = "FROM Category c WHERE c.name =\'category2\'";
        Query queryCategory2 = session.createQuery(queryStringCategory2);
        category2 = (Category) queryCategory2.getSingleResult();

        Expence expence = new Expence(category1);
        session.save(expence);

        String queryStringExpence = "FROM Expence e WHERE e.category.id =\'" + category1.getId() + "\'";
        Query queryExpence = session.createQuery(queryStringExpence);
        expence = (Expence) queryExpence.getSingleResult();

        user.setExpence(expence);
        session.save(user);


        tx.commit();

        session.close();

    }
}

```

Теперь можете проверить БД и увидеть записаные и связанные сущности.