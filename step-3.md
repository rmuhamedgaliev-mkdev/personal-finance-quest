# Инициализация Spring Boot приложения

Перед началом разработки необходимо создать пакет для Java приложения. Полное соглашение по именованию пакетов Java доступно по [ссылке](https://docs.oracle.com/javase/tutorial/java/package/namingpkgs.html)

Я создал пакеты со следующим названием: `io.github.rmuhamedgaliev`.

После этого наш проект выглядит следующим образом:

```
# rmuhamedgaliev @ MacBook-Pro-Rinat in ~/Projects/personal-finance on git:master x [0:33:26]
$ tree
.
├── bin
│   ├── main
│   │   └── App.class
│   └── test
│       └── AppTest.class
├── build.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
├── settings.gradle
└── src
    ├── main
    │   └── java
    │       └── io
    │           └── github
    │               └── rmuhamedgaliev
    │                   ├── App.java
    │                   ├── controller
    │                   │   └── GreetingController.java
    │                   └── model
    │                       └── Greeting.java
    └── test
        └── java
            └── AppTest.java
```

После того как мы создали пакеты, необходим добавить в проект зависимость Spring Boot. Посмотрите ниже на файл `build.gradle` и измените свой файл в соотвествии с примером.

```
/*
 * This file was generated by the Gradle 'init' task.
 *
 * This generated file contains a sample Java project to get you started.
 * For more details take a look at the Java Quickstart chapter in the Gradle
 * user guide available at https://docs.gradle.org/4.10.2/userguide/tutorial_java_projects.html
 */

plugins {
    // Apply the java plugin to add support for Java
    id 'java'

    // Apply the application plugin to add support for building an application
    id 'application'

    id 'org.springframework.boot' version '2.1.0.RELEASE'
    id 'io.spring.dependency-management' version '1.0.6.RELEASE'
}

// Define the main class for the application
mainClassName = 'io.github.rmuhamedgaliev.App'

bootJar {
    baseName = 'personal-finance-service'
    version =  '0.0.1'
}

sourceCompatibility = 11
targetCompatibility = 11

dependencies {
    // This dependency is found on compile classpath of this component and consumers.
    implementation 'com.google.guava:guava:23.0'

    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'

    // Use JUnit test framework
    testImplementation 'junit:junit:4.12'
}

// In this section you declare where to find the dependencies of your project
repositories {
    // Use jcenter for resolving your dependencies.
    // You can declare any Maven/Ivy/file repository here.
    jcenter()
}

wrapper {
    gradleVersion = '4.10.2' // Желаемая версия
}
```

Полная документация о добавлении в Java проект зависимости доступна по [ссылке](https://docs.gradle.org/current/userguide/building_java_projects.html#sec:java_dependency_management_overview).

Как добавили в зависимости Spring Boot добавим файлы.

GreetingController.java

```
package io.github.rmuhamedgaliev.controller;

import io.github.rmuhamedgaliev.model.Greeting;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import java.util.concurrent.atomic.AtomicLong;

@RestController
public class GreetingController {

    private static final String template = "Hello, %s!";
    private final AtomicLong counter = new AtomicLong();

    @RequestMapping("/greeting")
    public Greeting greeting(@RequestParam(value="name", defaultValue="World") String name) {
        return new Greeting(counter.incrementAndGet(),
                String.format(template, name));
    }
}
```

Greeting.java

```
package io.github.rmuhamedgaliev.model;

public class Greeting {

    private final long id;
    private final String content;

    public Greeting(long id, String content) {
        this.id = id;
        this.content = content;
    }

    public long getId() {
        return id;
    }

    public String getContent() {
        return content;
    }
}
```

App.java

```
package io.github.rmuhamedgaliev;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.SpringBootConfiguration;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;

/*
 * This Java source file was generated by the Gradle 'init' task.
 */
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan("io.github.rmuhamedgaliev")
public class App {

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

И если запустить проект, то получим в консоли следующее сообщение:

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.1.0.RELEASE)

2018-11-03 00:09:30.188  INFO 81950 --- [           main] io.github.rmuhamedgaliev.App             : Starting App on MacBook-Pro-Rinat.local with PID 81950 (/Users/rmuhamedgaliev/Projects/personal-finance/out/production/classes started by rmuhamedgaliev in /Users/rmuhamedgaliev/Projects/personal-finance)
2018-11-03 00:09:30.193  INFO 81950 --- [           main] io.github.rmuhamedgaliev.App             : No active profile set, falling back to default profiles: default
2018-11-03 00:09:31.181  INFO 81950 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2018-11-03 00:09:31.202  INFO 81950 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2018-11-03 00:09:31.203  INFO 81950 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/9.0.12
2018-11-03 00:09:31.212  INFO 81950 --- [           main] o.a.catalina.core.AprLifecycleListener   : The APR based Apache Tomcat Native library which allows optimal performance in production environments was not found on the java.library.path: [/Users/rmuhamedgaliev/Library/Java/Extensions:/Library/Java/Extensions:/Network/Library/Java/Extensions:/System/Library/Java/Extensions:/usr/lib/java:.]
2018-11-03 00:09:31.308  INFO 81950 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2018-11-03 00:09:31.308  INFO 81950 --- [           main] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1068 ms
2018-11-03 00:09:31.330  INFO 81950 --- [           main] o.s.b.w.servlet.ServletRegistrationBean  : Servlet dispatcherServlet mapped to [/]
2018-11-03 00:09:31.335  INFO 81950 --- [           main] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'characterEncodingFilter' to: [/*]
2018-11-03 00:09:31.335  INFO 81950 --- [           main] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
2018-11-03 00:09:31.335  INFO 81950 --- [           main] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'formContentFilter' to: [/*]
2018-11-03 00:09:31.336  INFO 81950 --- [           main] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'requestContextFilter' to: [/*]
2018-11-03 00:09:31.556  INFO 81950 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2018-11-03 00:09:31.733  INFO 81950 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2018-11-03 00:09:31.739  INFO 81950 --- [           main] io.github.rmuhamedgaliev.App             : Started App in 1.9 seconds (JVM running for 2.758)
2018-11-03 00:10:07.762  INFO 81950 --- [nio-8080-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
2018-11-03 00:10:07.763  INFO 81950 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
2018-11-03 00:10:07.769  INFO 81950 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 6 ms
```

Откройте в бразере [http://localhost:8080/greeting](http://localhost:8080/greeting) и получите сообщение

```
{"id":1,"content":"Hello, World!"}
```

Поздравляю, вы получили простой WEB сервер на Java.

Если вам необходимо получить исполняемый jar файл с вашим приложением выполните `./gradlew buid` и в папке `build/libs/` получите ваше приложение которое можно запустить согласно примеру в терминале `java -jar build/libs/personal-finance-service-0.0.1.jar `

Так же вы можете запустить ваше приложение при помощи Gradle комнды `./gradlew bootRun` и получите стандартный запуск вашего приложения.