I don't...
title: SpringBoot Auto-Assembly
Category: Frame
Tag:
- Spring Boot
I don't...

> Author: [Miki-byte-1024](https://github.com/Miki-byte-1024) & [Snailclimb](https://github.com/Snailclimb)

Every time I asked about Spring Boot, the interviewer liked to ask the question: "Tell me about Spring Boot's Auto-Configuration Method?"

I think we can answer from the following points:

1. What's Spring Boot auto-assembly?
2. How did Spring Boot achieve automatic assembly? How can we load on demand?
3. How can a Starter be achieved?

The length of the article is not deep, and small partners can go directly to the source code of the Spring Boot automatic assembly.

Foreword

A small partner who used Spring must have a fear of being dominated by XML configuration. Even if annotation-based configuration is introduced after Spring, we still need to use XML or Java for a visible configuration when opening some Spring features or introducing third-party dependencies.

Take an example. Without Spring Boot, we write a RestFul Web service, which requires the following configuration first.

```java
@Configuration
public class OtherConfig {
    @Bean
    public View jsonTemplate() {
        MappingJackson2JsonView view = new MappingJackson2JsonView();
        view.setPrettyPrint(true);
        return view;
    }

    @Bean
    public BeanNameViewResolver beanNameViewResolver() {
        return new BeanNameViewResolver();
    }
}
```

`spring-servlet.xml`

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context
                           http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/mvc
                           http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="com.howtodoinjava.demo"/>
    <mvc:annotation-driven />
</beans>
```

However, in the Spring Boot project, we just need to add the relevant dependency, without configuring, by starting with the `main` approach below.

```java
@SpringBootApplication
public class Main {
    public static void main(String[] args) {
        SpringApplication.run(Main.class, args);
    }
}
```

Furthermore, we can set up items such as changing port numbers, configuration of JPA properties, etc., through the global configuration file `application.properties` or `application.yml` of Spring Boot.

**Why does Spring Boot use so much source?** This benefits from its automatic assembly. **Auto-assembly can be described as the heart of Spring Boot. What is automatic assembly?**

# What's Spring Boot auto-assembly?

Now when we're talking about auto-assembly, it's usually linked to Spring Boot. But, actually, Spring Framework had that function long ago. Spring Boot was just built on its basis, further optimized by the way of SPI.

> Spring Boot defines a set of interface specifications that: Spring Boot scans the `META-INF/spring.factories` file in the jar package at startup, loads the type information that is configured in the file to the Spring container (this relates to the JVM loading mechanism and the Spring container knowledge) and performs the operations defined in the class. For external jars, you need to follow the criteria defined by Spring Boot to get your own functionality into Spring Boot.
> Starting with Spring Boot 3.0, the path of the automatically configured package has been modified from `META-INF/spring.factories` to `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`.

Without Spring Boot, if we need to introduce third-party dependencies, we need manual configuration, which is very troublesome. But in Spring Boot, we just introduce a starter. For example, if you want to use Redis in a project, you can just introduce the corresponding starter directly into the project.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

With the introduction of Starter, we can use the functions provided by third-party components with a few annotations and some simple configurations.

In my view, automatic assembly can simply be understood as **a piece of functionality that can be achieved with the help of Spring Boot by an annotation or some simple configuration.**

# Spring Boot: How does it make automatic assembly?

Let's look at the core annotation `@SpringBootApplication` for Spring Boot.

```java
@Target
@Retention
@Documented
@Inherited
public @