---
title: Spring Boot Auto-Configuration Principle Explained
category: Framework
tag:
  - SpringBoot
---

> Author: [Miki-byte-1024](https://github.com/Miki-byte-1024) & [Snailclimb](https://github.com/Snailclimb)

Every time Spring Boot is mentioned, interviewers are very fond of asking the question: "Can you explain the principle of Spring Boot's auto-configuration?"

I think we can answer from the following aspects:

1. What is Spring Boot auto-configuration?
1. How does Spring Boot achieve auto-configuration? How does it implement on-demand loading?
1. How to create a Starter?

Due to length constraints, this article does not delve deeply. Friends can also directly debug to see the source code for the Spring Boot auto-configuration part.

## Introduction

Those who have used Spring must be familiar with the fear of being ruled by XML configuration. Even with the introduction of annotation-based configuration in later versions of Spring, we still need explicit configuration using XML or Java when enabling certain Spring features or introducing third-party dependencies.

For example, before Spring Boot, when writing a RestFul web service, we first needed to configure as follows.

```java
@Configuration
public class RESTConfiguration
{
    @Bean
    public View jsonTemplate() {
        MappingJackson2JsonView view = new MappingJackson2JsonView();
        view.setPrettyPrint(true);
        return view;
    }

    @Bean
    public ViewResolver viewResolver() {
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
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context/ http://www.springframework.org/schema/context/spring-context.xsd
    http://www.springframework.org/schema/mvc/ http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="com.howtodoinjava.demo" />
    <mvc:annotation-driven />

    <!-- JSON Support -->
    <bean name="viewResolver" class="org.springframework.web.servlet.view.BeanNameViewResolver"/>
    <bean name="jsonTemplate" class="org.springframework.web.servlet.view.json.MappingJackson2JsonView"/>

</beans>
```

However, with a Spring Boot project, we only need to add the relevant dependencies and configure it simply by running the following `main` method.

```java
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

Moreover, we can use Spring Boot's global configuration files `application.properties` or `application.yml` to set up the project, such as changing the port number, configuring JPA properties, etc.

**Why is Spring Boot so enjoyable to use?** This is thanks to its auto-configuration. **Auto-configuration can be said to be the core of Spring Boot, so what exactly is auto-configuration?**

## What is Spring Boot Auto-Configuration?

When we talk about auto-configuration, we generally associate it with Spring Boot. However, in reality, the Spring Framework has long implemented this feature. Spring Boot only optimized it further based on this through SPI.

> Spring Boot defines a set of interface specifications that stipulate that during startup, Spring Boot will scan the `META-INF/spring.factories` files in the externally referenced jar packages, loading the type information configured in the files into the Spring container (this involves the JVM class loading mechanism and Spring's container knowledge), and performing various operations defined in those classes. For external jars, they just need to follow the standards defined by Spring Boot to integrate their capabilities into Spring Boot.
> Since Spring Boot 3.0, the path for the auto-configuration package has changed from `META-INF/spring.factories` to `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`.

Without Spring Boot, if we need to introduce a third-party dependency, we would have to configure it manually, which is very cumbersome. However, in Spring Boot, we can simply include a starter. For example, if you want to use Redis in your project, you can directly include the corresponding starter.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

After introducing the starter, we can use the functionalities provided by third-party components with just a few annotations and some simple configurations.

In my view, auto-configuration can be simply understood as: **achieving a certain functionality with the help of Spring Boot through annotations or some simple configurations.**

## How does Spring Boot achieve auto-configuration?

Let's first look at Spring Boot's core annotation `SpringBootApplication`.

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
<1.>@SpringBootConfiguration
<2.>@ComponentScan
<3.>@EnableAutoConfiguration
public @interface SpringBootApplication {

}

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration // In fact, it is also a configuration class
public @interface SpringBootConfiguration {
}
```

You can roughly consider `@SpringBootApplication` as a combination of the `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan` annotations. According to the official Spring Boot website, the functions of these three annotations are:

- `@EnableAutoConfiguration`: Enables Spring Boot's auto-configuration mechanism.
- `@Configuration`: Allows additional beans to be registered in the context or other configuration classes to be imported.
- `@ComponentScan`: Scans beans annotated with `@Component` (`@Service`, `@Controller`), the annotation will, by default, scan all classes under the package of the startup class, and it is possible to customize to exclude certain beans. As shown in the figure, `TypeExcludeFilter` and `AutoConfigurationExcludeFilter` will exclude from the container.

![](https://oss.javaguide.cn/p3-juejin/bcc73490afbe4c6ba62acde6a94ffdfd~tplv-k3u1fbpfcp-watermark.png)

`@EnableAutoConfiguration` is the key annotation for implementing auto-configuration. Let's start with this annotation.

### @EnableAutoConfiguration: The core annotation that implements auto-configuration

`EnableAutoConfiguration` is just a simple annotation; the core functionality of auto-configuration is implemented through the `AutoConfigurationImportSelector` class.

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage // Function: Registers all components under the main package in the container
@Import({AutoConfigurationImportSelector.class}) // Loads auto-configuration classes xxxAutoconfiguration
public @interface EnableAutoConfiguration {
    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

    Class<?>[] exclude() default {};

    String[] excludeName() default {};
}
```

Now, let's analyze what the `AutoConfigurationImportSelector` class actually does.

### AutoConfigurationImportSelector: Loads auto-configuration classes

The inheritance hierarchy of the `AutoConfigurationImportSelector` class is as follows:

```java
public class AutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware, ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {

}

public interface DeferredImportSelector extends ImportSelector {

}

public interface ImportSelector {
    String[] selectImports(AnnotationMetadata var1);
}
```

It can be seen that the `AutoConfigurationImportSelector` class implements the `ImportSelector` interface, thereby implementing the `selectImports` method in this interface, which is mainly used to **retrieve the fully qualified class names of all classes that meet the conditions, which need to be loaded into the IoC container**.

```java
private static final String[] NO_IMPORTS = new String[0];

public String[] selectImports(AnnotationMetadata annotationMetadata) {
        // <1>. Check if the auto-configuration switch is enabled
        if (!this.isEnabled(annotationMetadata)) {
            return NO_IMPORTS;
        } else {
          //<2>. Retrieve all beans that need to be configured
            AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader.loadMetadata(this.beanClassLoader);
            AutoConfigurationImportSelector.AutoConfigurationEntry autoConfigurationEntry = this.getAutoConfigurationEntry(autoConfigurationMetadata, annotationMetadata);
            return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
        }
    }
```

Here we need to focus on the `getAutoConfigurationEntry()` method, which is primarily responsible for loading auto-configuration classes.

The method call chain is as follows:

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/3c1200712655443ca4b38500d615bb70~tplv-k3u1fbpfcp-watermark.png)

Now let's analyze the `getAutoConfigurationEntry()` source code in detail:

```java
private static final AutoConfigurationEntry EMPTY_ENTRY = new AutoConfigurationEntry();

AutoConfigurationEntry getAutoConfigurationEntry(AutoConfigurationMetadata autoConfigurationMetadata, AnnotationMetadata annotationMetadata) {
        //<1>.
        if (!this.isEnabled(annotationMetadata)) {
            return EMPTY_ENTRY;
        } else {
            //<2>.
            AnnotationAttributes attributes = this.getAttributes(annotationMetadata);
            //<3>.
            List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
            //<4>.
            configurations = this.removeDuplicates(configurations);
            Set<String> exclusions = this.getExclusions(annotationMetadata, attributes);
            this.checkExcludedClasses(configurations, exclusions);
            configurations.removeAll(exclusions);
            configurations = this.filter(configurations, autoConfigurationMetadata);
            this.fireAutoConfigurationImportEvents(configurations, exclusions);
            return new AutoConfigurationImportSelector.AutoConfigurationEntry(configurations, exclusions);
        }
    }
```

**Step 1**:

Check if the auto-configuration switch is enabled. By default, `spring.boot.enableautoconfiguration=true`, which can be set in `application.properties` or `application.yml`.

![](https://oss.javaguide.cn/p3-juejin/77aa6a3727ea4392870f5cccd09844ab~tplv-k3u1fbpfcp-watermark.png)

**Step 2**:

Used to retrieve `exclude` and `excludeName` from the `EnableAutoConfiguration` annotation.

![](https://oss.javaguide.cn/p3-juejin/3d6ec93bbda1453aa08c52b49516c05a~tplv-k3u1fbpfcp-zoom-1.png)

**Step 3**

Get all configuration classes that need to be auto-configured by reading `META-INF/spring.factories`.

```plain
spring-boot/spring-boot-project/spring-boot-autoconfigure/src/main/resources/META-INF/spring.factories
```

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/58c51920efea4757aa1ec29c6d5f9e36~tplv-k3u1fbpfcp-watermark.png)

From the figure below you can see that the configuration contents of this file have all been loaded. The `XXXAutoConfiguration` functions to load components as needed.

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/94d6e1a060ac41db97043e1758789026~tplv-k3u1fbpfcp-watermark.png)

Not only is the `META-INF/spring.factories` of this dependency read, but all `META-INF/spring.factories` under Spring Boot Starters will also be read.

So, you can clearly see that the Spring Boot Starter for the Druid database connection pool creates a `META-INF/spring.factories` file.

If we want to create a Spring Boot Starter ourselves, this step is essential.

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/68fa66aeee474b0385f94d23bcfe1745~tplv-k3u1fbpfcp-watermark.png)

**Step 4**:

At this point, the interviewer might ask you, "Do we have to load all the configurations in `spring.factories` every time we start?"

Clearly, this is not practical. If you debug further, you will find that the value of `configurations` gets smaller.

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/267f8231ae2e48d982154140af6437b0~tplv-k3u1fbpfcp-watermark.png)

This is because there has been a filtering step; only classes with all conditions in `@ConditionalOnXXX` will take effect.

```java
@Configuration
// Check if the related classes: RabbitTemplate and Channel exist
// Only load if they exist
@ConditionalOnClass({ RabbitTemplate.class, Channel.class })
@EnableConfigurationProperties(RabbitProperties.class)
@Import(RabbitAnnotationDrivenConfiguration.class)
public class RabbitAutoConfiguration {
}
```

Interested readers can learn more about the conditional annotations provided by Spring Boot.

- `@ConditionalOnBean`: Under the condition that a specified Bean is present in the container.
- `@ConditionalOnMissingBean`: When a specified Bean is not present in the container.
- `@ConditionalOnSingleCandidate`: When a specified Bean is the only one in the container, or if there are multiple but the specified one is the preferred Bean.
- `@ConditionalOnClass`: When a specified class is present on the classpath.
- `@ConditionalOnMissingClass`: When a specified class is not present on the classpath.
- `@ConditionalOnProperty`: Whether a specified property has a given value.
- `@ConditionalOnResource`: Whether a specified resource exists in the classpath.
- `@ConditionalOnExpression`: Based on SpEL expressions as conditional judgments.
- `@ConditionalOnJava`: Based on Java version as conditional judgments.
- `@ConditionalOnJndi`: When JNDI exists at the specified location.
- `@ConditionalOnNotWebApplication`: When the current project is not a web application.
- `@ConditionalOnWebApplication`: When the current project is a web application.

## How to Create a Starter

It's all talk and no action. Let's create a starter to implement a custom thread pool.

Step 1: Create the `threadpool-spring-boot-starter` project

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/1ff0ebe7844f40289eb60213af72c5a6~tplv-k3u1fbpfcp-watermark.png)

Step 2: Introduce Spring Boot related dependencies

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/5e14254276604f87b261e5a80a354cc0~tplv-k3u1fbpfcp-watermark.png)

Step 3: Create `ThreadPoolAutoConfiguration`

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/1843f1d12c5649fba85fd7b4e4a59e39~tplv-k3u1fbpfcp-watermark.png)

Step 4: In the `threadpool-spring-boot-starter` project's resources package, create the `META-INF/spring.factories` file

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/97b738321f1542ea8140484d6aaf0728~tplv-k3u1fbpfcp-watermark.png)

Finally, create a new project and include `threadpool-spring-boot-starter`

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/edcdd8595a024aba85b6bb20d0e3fed4~tplv-k3u1fbpfcp-watermark.png)

Test passed!!!

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/9a265eea4de742a6bbdbbaa75f437307~tplv-k3u1fbpfcp-watermark.png)

## Conclusion

Spring Boot enables auto-configuration through `@EnableAutoConfiguration` and ultimately loads the auto-configuration classes from `META-INF/spring.factories` via SpringFactoriesLoader. The auto-configuration classes are essentially configuration classes loaded on-demand via `@Conditional`; to take effect, the `spring-boot-starter-xxx` package must be included to establish the dependency.
