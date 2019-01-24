# Знакомство с Spring Data (JPA)

В предыдущих квестах мы уже познакомились с референсной имплементацией [JPA Java Hibernate](http://hibernate.org/orm/). Для фреймворка Spring существует адаптация в виде [Spring Data](https://spring.io/projects/spring-data). Он позволяет более гибче работать с базой данных. Для данного квеста необходимы базовое понимание `Hibernate`.

Для подключения `Spring Data` следуйте следующим шагам:

## Добавление зависимости

Добавьте в `gradle` следующие зависимости.

- compile group: 'org.postgresql', name: 'postgresql', version: '42.2.5'
- compile group: 'org.springframework.boot', name: 'spring-boot-starter-data-jpa', version: '2.1.2.RELEASE'

## Конфигурация Spring

Spring приложения в большинстве случаев конфигурируются при помощи файла [application.properties](https://docs.spring.io/spring-boot/docs/current/reference/html/common-application-properties.html).

Добавьте в файл `application.properties` следующую конфигурацию

```
## Spring DATASOURCE (DataSourceAutoConfiguration & DataSourceProperties)
# JDBC URL
spring.datasource.url=jdbc:postgresql://localhost:5432/repairauto
# Имя пользователя
spring.datasource.username= postgres
# Пароль от БД
spring.datasource.password=123
# Включить отладочную демонстрацию SQL запросов
spring.jpa.show-sql=true

# The SQL dialect makes Hibernate generate better SQL for the chosen database
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.PostgreSQLDialect

# Hibernate ddl auto (create, create-drop, validate, update)
spring.jpa.hibernate.ddl-auto = create-drop

# Logging
logging.level.org.hibernate.SQL=DEBUG
logging.level.org.hibernate.type.descriptor.sql.BasicBinder=TRACE
```

## Создаем интерфейс для репозитория

Spring Data содержить базовые репозитории для работы с домменными моделями. Полное описание доступно в [документации](https://docs.spring.io/spring-data/data-commons/docs/1.6.1.RELEASE/reference/html/repositories.html).

В данном задании воспользуемся `JpaRepository`. Создаем интерфейс и расширяем его от `JpaRepository`, где используем как модель доменной области `Models`, а первичный ключ `Long`.

```java
package io.github.personal_finance.repos;


import io.github.personal_finance.domain.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UsersRepository extends JpaRepository<User, Long> {


    User findUserById(Long id);
}

```

## Вызов репозитория

Теперь подключим репозиторий и попытаемся его вызывать:

```java
package io.github.personal_finance.controller;

import io.github.personal_finance.controller.dto.UserCreateDTO;
import io.github.personal_finance.controller.dto.UserUpdateDTO;
import io.github.personal_finance.domain.User;
import io.github.personal_finance.repos.UsersRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;


@RestController
@RequestMapping("/users")
public class UsersController {

    private UsersRepository usersRepository;

    @Autowired
    public UsersController(UsersRepository usersRepository) {
        this.usersRepository = usersRepository;
    }

    @ResponseBody
    @GetMapping
    public List<User> getAllUsers() {
        List<User> users = this.usersRepository.findAll();

        return users;
    }

    @ResponseBody
    @GetMapping(value = "/{id}")
    public User getUserById(@PathVariable(value = "id") Long id) {
        User user = this.usersRepository.findUserById(id);

        return user;
    }

    @ResponseBody
    @PutMapping(value = "/{id}")
    public User updateUserById(@PathVariable(value = "id") Long id, @RequestBody UserUpdateDTO userUpdateDTO) {
        User user = this.usersRepository.findUserById(id);

        user.setName(userUpdateDTO.getUsername());

        user = this.usersRepository.save(user);

        return user;
    }

    @ResponseBody
    @DeleteMapping(value = "/{id}")
    public void updateUserById(@PathVariable(value = "id") Long id) {
        this.usersRepository.deleteById(id);
    }

    @ResponseBody
    @PostMapping
    public User createUser(@RequestBody UserCreateDTO userCreateDTO) {
        User user = new User(userCreateDTO.getUsername());
        user = this.usersRepository.save(user);
        return user;
    }

}

```

Отправляем тестовый запрос по HTTP и смотрим в консоль:

А в консоли теримнала приложения вы можете увидеть примерно такой ответ:

```bash
2018-12-02 01:37:46.679 INFO 23924 --- [ main] o.s.j.e.a.AnnotationMBeanExporter : Registering beans for JMX exposure on startup
2018-12-02 01:37:46.681 INFO 23924 --- [ main] o.s.j.e.a.AnnotationMBeanExporter : Bean with name 'dataSource' has been autodetected for JMX exposure
2018-12-02 01:37:46.687 INFO 23924 --- [ main] o.s.j.e.a.AnnotationMBeanExporter : Located MBean 'dataSource': registering with JMX server as MBean [com.zaxxer.hikari:name=dataSource,type=HikariDataSource]
2018-12-02 01:37:46.726 INFO 23924 --- [ main] o.s.b.w.embedded.tomcat.TomcatWebServer : Tomcat started on port(s): 8080 (http) with context path ''
2018-12-02 01:37:46.731 INFO 23924 --- [ main] io.repairbase.App : Started App in 4.437 seconds (JVM running for 10.346)
2018-12-02 01:38:00.465 INFO 23924 --- [nio-8080-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/] : Initializing Spring FrameworkServlet 'dispatcherServlet'
2018-12-02 01:38:00.465 INFO 23924 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet : FrameworkServlet 'dispatcherServlet': initialization started
2018-12-02 01:38:00.481 INFO 23924 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet : FrameworkServlet 'dispatcherServlet': initialization completed in 15 ms
2018-12-02 01:38:00.680 DEBUG 23924 --- [nio-8080-exec-1] org.hibernate.SQL : select nextval ('model_sequence')
Hibernate: select nextval ('model_sequence')
2018-12-02 01:38:00.690 DEBUG 23924 --- [nio-8080-exec-1] org.hibernate.SQL : select nextval ('model_sequence')
Hibernate: select nextval ('model_sequence')
2018-12-02 01:38:00.706 DEBUG 23924 --- [nio-8080-exec-1] org.hibernate.SQL : insert into models (id_model, name, id) values (?, ?, ?)
Hibernate: insert into models (id_model, name, id) values (?, ?, ?)
2018-12-02 01:38:00.709 TRACE 23924 --- [nio-8080-exec-1] o.h.type.descriptor.sql.BasicBinder : binding parameter [1] as [INTEGER] - [null]
2018-12-02 01:38:00.709 TRACE 23924 --- [nio-8080-exec-1] o.h.type.descriptor.sql.BasicBinder : binding parameter [2] as [VARCHAR] - [Test]
2018-12-02 01:38:00.709 TRACE 23924 --- [nio-8080-exec-1] o.h.type.descriptor.sql.BasicBinder : binding parameter [3] as [BIGINT] - [1]
2018-12-02 01:38:01.939 DEBUG 23924 --- [nio-8080-exec-3] org.hibernate.SQL : insert into models (id_model, name, id) values (?, ?, ?)
Hibernate: insert into models (id_model, name, id) values (?, ?, ?)
2018-12-02 01:38:01.939 TRACE 23924 --- [nio-8080-exec-3] o.h.type.descriptor.sql.BasicBinder : binding parameter [1] as [INTEGER] - [null]
2018-12-02 01:38:01.939 TRACE 23924 --- [nio-8080-exec-3] o.h.type.descriptor.sql.BasicBinder : binding parameter [2] as [VARCHAR] - [Test]
2018-12-02 01:38:01.940 TRACE 23924 --- [nio-8080-exec-3] o.h.type.descriptor.sql.BasicBinder : binding parameter [3] as [BIGINT] - [2]
2018-12-02 01:38:02.610 DEBUG 23924 --- [nio-8080-exec-4] org.hibernate.SQL : insert into models (id_model, name, id) values (?, ?, ?)
Hibernate: insert into models (id_model, name, id) values (?, ?, ?)
2018-12-02 01:38:02.610 TRACE 23924 --- [nio-8080-exec-4] o.h.type.descriptor.sql.BasicBinder : binding parameter [1] as [INTEGER] - [null]
2018-12-02 01:38:02.610 TRACE 23924 --- [nio-8080-exec-4] o.h.type.descriptor.sql.BasicBinder : binding parameter [2] as [VARCHAR] - [Test]
2018-12-02 01:38:02.611 TRACE 23924 --- [nio-8080-exec-4] o.h.type.descriptor.sql.BasicBinder : binding parameter [3] as [BIGINT] - [3]
2018-12-02 01:38:03.370 DEBUG 23924 --- [nio-8080-exec-5] org.hibernate.SQL : insert into models (id_model, name, id) values (?, ?, ?)
Hibernate: insert into models (id_model, name, id) values (?, ?, ?)
2018-12-02 01:38:03.370 TRACE 23924 --- [nio-8080-exec-5] o.h.type.descriptor.sql.BasicBinder : binding parameter [1] as [INTEGER] - [null]
2018-12-02 01:38:03.370 TRACE 23924 --- [nio-8080-exec-5] o.h.type.descriptor.sql.BasicBinder : binding parameter [2] as [VARCHAR] - [Test]
2018-12-02 01:38:03.370 TRACE 23924 --- [nio-8080-exec-5] o.h.type.descriptor.sql.BasicBinder : binding parameter [3] as [BIGINT] - [4]
2018-12-02 01:38:03.986 DEBUG 23924 --- [nio-8080-exec-6] org.hibernate.SQL : insert into models (id_model, name, id) values (?, ?, ?)
Hibernate: insert into models (id_model, name, id) values (?, ?, ?)
2018-12-02 01:38:03.987 TRACE 23924 --- [nio-8080-exec-6] o.h.type.descriptor.sql.BasicBinder : binding parameter [1] as [INTEGER] - [null]
2018-12-02 01:38:03.987 TRACE 23924 --- [nio-8080-exec-6] o.h.type.descriptor.sql.BasicBinder : binding parameter [2] as [VARCHAR] - [Test]
2018-12-02 01:38:03.987 TRACE 23924 --- [nio-8080-exec-6] o.h.type.descriptor.sql.BasicBinder : binding parameter [3] as [BIGINT] - [5]
2018-12-02 01:58:40.482 DEBUG 23924 --- [nio-8080-exec-9] org.hibernate.SQL : insert into models (id_model, name, id) values (?, ?, ?)
Hibernate: insert into models (id_model, name, id) values (?, ?, ?)
2018-12-02 01:58:40.482 TRACE 23924 --- [nio-8080-exec-9] o.h.type.descriptor.sql.BasicBinder : binding parameter [1] as [INTEGER] - [null]
2018-12-02 01:58:40.482 TRACE 23924 --- [nio-8080-exec-9] o.h.type.descriptor.sql.BasicBinder : binding parameter [2] as [VARCHAR] - [Test]
2018-12-02 01:58:40.482 TRACE 23924 --- [nio-8080-exec-9] o.h.type.descriptor.sql.BasicBinder : binding parameter [3] as [BIGINT] - [6]
2018-12-02 01:59:10.723 WARN 23924 --- [io-8080-exec-10] .w.s.m.s.DefaultHandlerExceptionResolver : Resolved [org.springframework.web.HttpRequestMethodNotSupportedException: Request method 'GET' not supported]
2018-12-02 01:59:44.988 DEBUG 23924 --- [nio-8080-exec-1] org.hibernate.SQL : insert into models (id_model, name, id) values (?, ?, ?)
Hibernate: insert into models (id_model, name, id) values (?, ?, ?)
2018-12-02 01:59:44.989 TRACE 23924 --- [nio-8080-exec-1] o.h.type.descriptor.sql.BasicBinder : binding parameter [1] as [INTEGER] - [null]
2018-12-02 01:59:44.989 TRACE 23924 --- [nio-8080-exec-1] o.h.type.descriptor.sql.BasicBinder : binding parameter [2] as [VARCHAR] - [test]
2018-12-02 01:59:44.989 TRACE 23924 --- [nio-8080-exec-1] o.h.type.descriptor.sql.BasicBinder : binding parameter [3] as [BIGINT] - [7]
```

Напишите запрос для остальных сущностей:

- Category
- Expence