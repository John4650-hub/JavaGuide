---
title: Summary of Common Annotations in Spring & SpringBoot
category: Framework
tag:
  - SpringBoot
  - Spring
---

### 0. Introduction

It is no exaggeration to say that the common annotations introduced in this article for Spring/SpringBoot cover most of the common scenarios you will encounter in your work. For each annotation, I have explained its specific usage. Once you grasp these, you should have no major issues using SpringBoot to develop projects!

**Why write this article?**

Recently, I saw an article about common SpringBoot annotations that was widely shared online. After reading the content, I felt that the quality was quite low and could mislead those with little practical experience (who make up the majority). Therefore, I took about two days to summarize the key points.

**Due to my limited ability and energy, if there are any inaccuracies or areas that need improvement, please point them out! I would be very grateful!**

### 1. `@SpringBootApplication`

Let's first highlight the `@SpringBootApplication` annotation, even though we generally do not use it actively.

_Guide: This annotation is the cornerstone of a Spring Boot project and is automatically added to the main class after creating a SpringBoot project._

```java
@SpringBootApplication
public class SpringSecurityJwtGuideApplication {
      public static void main(java.lang.String[] args) {
        SpringApplication.run(SpringSecurityJwtGuideApplication.class, args);
    }
}
```

We can think of `@SpringBootApplication` as a combination of the `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan` annotations.

```java
package org.springframework.boot.autoconfigure;
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
    @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
    @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
   ......
}

package org.springframework.boot;
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {

}
```

According to the SpringBoot official website, the functions of these three annotations are as follows:

- `@EnableAutoConfiguration`: Enables SpringBoot's auto-configuration mechanism.
- `@ComponentScan`: Scans for beans annotated with `@Component` (`@Repository`, `@Service`, `@Controller`), and by default, it scans all classes in the package where the annotated class is located.
- `@Configuration`: Allows registration of additional beans in the Spring context or importing other configuration classes.

### 2. Related to Spring Beans

#### 2.1. `@Autowired`

Automatically imports objects into a class. The injected class must also be managed by the Spring container, for example: injecting a Service class into a Controller class.

```java
@Service
public class UserService {
  ......
}

@RestController
@RequestMapping("/users")
public class UserController {
   @Autowired
   private UserService userService;
   ......
}
```

#### 2.2. `@Component`, `@Repository`, `@Service`, `@Controller`

We generally use the `@Autowired` annotation to let the Spring container automatically wire beans for us. To mark a class as a bean that can be automatically wired with the `@Autowired` annotation, we can use the following annotations:

- `@Component`: A general annotation that can mark any class as a Spring component. If a bean does not belong to a specific layer, you can use the `@Component` annotation.
- `@Repository`: Corresponds to the persistence layer, i.e., the Dao layer, mainly used for database-related operations.
- `@Service`: Corresponds to the service layer, mainly involving some complex logic that requires the Dao layer.
- `@Controller`: Corresponds to the Spring MVC controller layer, mainly used to accept user requests and call the service layer to return data to the front-end page.

#### 2.3. `@RestController`

The `@RestController` annotation is a combination of `@Controller` and `@ResponseBody`, indicating that this is a controller bean, and the return value of the function is directly placed into the HTTP response body, making it a REST-style controller.

_Guide: Nowadays, with front-end and back-end separation, I honestly haven't used `@Controller` in a long time. If your project is too old, just ignore what I said._

Using `@Controller` alone without `@ResponseBody` is generally used in cases where a view is to be returned, which is a more traditional application of Spring MVC, corresponding to scenarios where the front-end and back-end are not separated. `@Controller` + `@ResponseBody` returns data in JSON or XML format.

For a comparison between `@RestController` and \`@
