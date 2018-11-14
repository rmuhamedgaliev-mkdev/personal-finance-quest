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
        <property name="connection.url">jdbc:postgresql://localhost:5432/DB</property> // имя базы данных
        <property name="connection.username">postgres</property> // имя пользователя
        <property name="connection.password">123</property> // пароль
        <property name="dialect">org.hibernate.dialect.PostgreSQL95Dialect</property>
        <property name="default_schema">public</property> // схема БД
        <property name="swow_sql">true</property> // отображенеи SQL запросов
        <property name="hbm2ddl.auto">create-drop</property> // тип генерации 

        <mapping class="autobase.models.Models"/> // путь до медли БД. Можн дописать потом

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

![image](https://github.com/rmuhamedgaliev-mkdev/repair-auto-quest/blob/master/er-diagram.png?raw=true)

Связать сущности необходимо в двусторонней связи как в [примере](http://fruzenshtein.com/bidirectional-many-to-one-association/).

```java
package autobase;

import javax.persistence.*;
import java.io.Serializable;

@Entity
@Table(name = "models")
public class CarModel implements Serializable {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "ID")
    private Integer modelID;
    @Column(name = "name")
    private String name;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "ID_MODEL")
    private Manufacture manufacture;

    public CarModel() {
    }

    public CarModel(String name) {
        this.name = name;
    }

    public CarModel(Integer modelID, String name) {
        this.modelID = modelID;
        this.name = name;
    }

    public Integer getModelID() {
        return modelID;
    }

    public void setModelID(Integer modelID) {
        this.modelID = modelID;
    }

    public String getName() {
        return name;
    }

    public Manufacture getManufacture() {
        return manufacture;
    }

    public void setManufacture(Manufacture manufacture) {
        this.manufacture = manufacture;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

```java
package autobase;

import javax.persistence.*;
import java.math.BigInteger;
import java.util.List;

@Entity
@Table(name = "manufacture")
public class Manufacture {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "ID")
    private Integer ID;

    @OneToMany(fetch = FetchType.LAZY, mappedBy = "manufacture")
    @Column(name = "ID_MODEL")
    private List<CarModel> carModels;

    @Column(name = "manufacturer_name")
    private String name;

    public Manufacture() {
    }

    public Manufacture(List<CarModel> carModels, String name) {
        this.carModels = carModels;
        this.name = name;
    }

    public Integer getID() {
        return ID;
    }

    public void setID(Integer ID) {
        this.ID = ID;
    }

    public List<CarModel> getCarModels() {
        return carModels;
    }

    public void setCarModels(List<CarModel> carModels) {
        this.carModels = carModels;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

```

```java
public class Main {
    public static void main(String[] args) {
        SessionFactory sessionFactory = HibernateUtil.getSessionFactory();

        Session session = sessionFactory.openSession();
        Transaction tx = session.beginTransaction();

        CarModel carModel = new CarModel( "model_name");
        carModel.setName("rio");
        session.save(carModel);
        tx.commit();

        String queryString = "FROM CarModel cm WHERE cm.name =\'rio\'";
        Query query = session.createQuery(queryString);
        carModel = (CarModel) query.getSingleResult();

        System.out.println("ID: "+carModel.getModelID()+"\n"+"Model: "+carModel.getName()+"\n");

        Transaction txM = session.beginTransaction();

        Manufacture manufacture = new Manufacture(Arrays.asList(carModel), "RioYo");
        session.save(manufacture);
        txM.commit();


        tx = session.beginTransaction();
        carModel.setManufacture(manufacture);
        tx.commit();
        
        session.close();

    }
}
```