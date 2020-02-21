## Spring Boot

#### 1 Adding Spring Boot to project
Gradle example:

```groovy
...
plugins {
	id 'org.springframework.boot' version '2.1.0.RELEASE'
}
...
dependencies {
  implementation ("org.springframework.boot:spring-boot-starter-actuator")
  implementation ("org.springframework.boot:spring-boot-starter-data-jpa")
  implementation ("org.springframework.boot:spring-boot-starter-security")
  implementation ("org.springframework.boot:spring-boot-starter-web")
  ...
}
```
Most Spring libraries are imported into the project with the use of simple Boot "starters".

The Spring Boot Gradle Plugin provides
- package executable jar or war archives, run Spring Boot applications
- use the dependency management provided by `spring-boot-dependencies`, i.e. it allows omitting versions when declaring `spring-boot-starter-*` dependencies.

#### 2 @SpringBootApplication
`@SpringBootApplication` is equivalent to [`@Configuration`, `@EnableAutoConfiguration`, `@ComponentScan`].
 - `@EnableAutoConfiguration` tells Spring Boot to "guess" how you want to configure Spring, based on the jar dependencies that you have on class path.
 - `@ComponentScan(basePackages="com...")` will automatically pick up all Spring components, including `@Configuration` classes.
 - `@Configuration` allow to register extra beans in the context or import additional configuration classes.

#### 3 @SpringBootTest
`@SpringBootTest` is used for testing to load the application context.

#### 4 Properties
The default property File is `application.properties`. The expected locations are:
- a `/config` subdirectory of the current directory
- the current directory
- a `/config` directory on the classpath
- the classpath root

In addition, profile-specific properties can also be defined e.g: `application-{profile}.properties`.
This env file will be loaded and will take precedence over the default property file.

YAML files are also supported.

As opposed to using files, properties can be passed directly on the command line:
`java -jar app.jar --property="value` 
or via system properties:
`java -Dproperty.name="value" -jar app.jar`.

Spring Boot will also detect **environment variables**, treating them as properties.

#### 5 @PropertySource
 Allows adding property sources to the environment, e.g:
 ```java
@Configuration
@PropertySource("classpath:foo.properties")
public class PropertiesWithJavaConfig {
    //...
}
```

#### 6 @Value
Injecting a property with the @Value annotation:
```java
@Value( "${jdbc.url}" )
private String jdbcUrl;
```
