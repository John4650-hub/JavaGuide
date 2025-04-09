I don't...
Title: Spring & Spring Boot Common Note Summary
Category: Frame
Tag:

- Spring Boot.
- Spring.
  I don't...

# 0 Foreword

It is no exaggeration to say that most of the common scenes you encounter in your work are already covered by the Spring/Spring Boot common note. For every note, I told you how to use it, understand it, and use Spring Boot to develop a project.

**Why write this article?**

More recently, an article on Spring Boot's frequently used annotations has been seen on the Internet, and I actually feel a little low quality when I look at it, and a little misleading to those who do not have much practical experience (who in turn dominate). So it took me about two days to sum it up.

**Because of my limited personal abilities and energy, please point out if anything is wrong or needs improvement! Guide, thank you so much!**

### 1. `@SpringBootApplication`

The `@SpringBootApplication` annotation is one that we do not normally use on our own initiative.

_Guide: This annotation is the cornerstone of the Spring Boot project, which will be added to the main category by default after it is created._

```java
@SpringBootApplication
public class SpringSecureJwtGuideApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringSecureJwtGuideApplication.class, args);
    }
}
```

We can see `@SpringBootApplication` as a collection of `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan` annotations.

```java
@Target
@Retention
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class)
@interface SpringBootApplication {}
```

According to Spring Boot, the roles of the three annotations are:

- `@EnableAutoConfiguration`: Enable Spring Boot
- `@ComponentScan`: Scanning is noted by `@Component` (`@Repository`, `@Service`, `@Controller`) and the annotation by default scans all categories under the package where the class is located.
- `@Configuration`: Allows the registration of additional beans or the import of other configurations in the Spring context.

## 2. Spring Bean Related

### 2.1 `@Autowired`

Automatically import an object into a class; the category that is injected is also managed by the Spring container, e.g., the Service class into the Controller class.

```java
@Service
public class UserService {
    // Service logic
}

@RestController
@RequestMapping("/users")
public class UserController {
    @Autowired
    private UserService userService;
}
```

### 2.2 `@Component`, `@Repository`, `@Service`, `@Controller`

We usually use the `@Autowired` annotation to enable Spring to auto-assemble our beans. In order to make class identification a class that can be used for `@Autowired` auto-assembly, the following annotations could be used:

- `@Component`: A generic annotation to mark any class as a Spring component. If a bean does not know which level it belongs to, it can use `@Component` to unmark.
- `@Repository`: Corresponds to a persistent layer, the DAO layer, which is used mainly for database-related operations.
- `@Service`: Corresponds to the Service layer, which mainly involves complex logic and needs to be used on the DAO level.
- `@Controller`: Corresponds to the Spring MVC control layer, which is used primarily to receive user requests and call Service to return data to the front page.

### 2.3 `@RestController`

`@RestController` is a combination of `@Controller` and `@ResponseBody`, indicating that it is a controller bean and that the return value of the function is entered directly in the HTTP response and is a controller in the REST style.

_Guide: It's all back-to-back, and to be honest, I haven't used it for a long time. If your project is too old, forget it._

The separate use of `@Controller` without `@ResponseBody` is generally used in cases where a view is to be returned, a more traditional application of Spring MVC, which corresponds to a situation of non-separation. `@Controller` + `@ResponseBody` returns JSON or XML data.

For a comparison of `@RestController` and `@Controller`, see this article: \[@RestController vs @Controller\](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA=&mid=2247485544&idx=1&sn=3cc95b8897e28fe3bfe539eb4d8&chksm=cea24ff4b8697adc3e828ecf71a346845e70221cce76d1e7285359907&token=17250912&lang=zh_CN
