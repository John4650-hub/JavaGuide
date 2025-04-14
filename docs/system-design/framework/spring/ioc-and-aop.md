---
title: Explanation of IoC & AOP (Quick Understanding)
category: Framework
tag:
  - Spring
---

This article will explain IoC & AOP through the following questions:

- What is IoC?
- What problem does IoC solve?
- What is the difference between IoC and DI?
- What is AOP?
- What problem does AOP solve?
- What are the application scenarios of AOP?
- Why is AOP called aspect-oriented programming?
- What are the ways to implement AOP?

First, it should be noted that IoC & AOP were not introduced by Spring; they actually existed before Spring but were more theoretical at that time. Spring has implemented these two ideas very well at a technical level.

## IoC (Inversion of Control)

### What is IoC?

IoC (Inversion of Control) refers to the inversion of control. It is a concept rather than a technical implementation, describing the issue of object creation and management in the Java development field.

For example, a class A depends on a class B:

- **Traditional development approach**: Typically, class A manually creates an object of class B using the new keyword.
- **Development approach using IoC**: Instead of creating an object through the new keyword, we use the IoC container (Spring framework) to help instantiate objects. We can simply retrieve the object we need directly from the IoC container.

From the comparison of these two development approaches, we "lose a power" (the power to create and manage objects) and gain a benefit (no longer needing to consider the creation, management, and related matters of objects).

**Why is it called inversion of control?**

- **Control**: Refers to the power of creating (instantiating and managing) objects.
- **Inversion**: The control is handed over to an external environment (IoC container).

![IoC Illustration](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/IoC&Aop-ioc-illustration.png)

### What problem does IoC solve?

The idea of IoC is that two parties do not rely on one another; instead, a third-party container manages the related resources. What are the benefits?

1. Reduces the coupling degree or dependency between objects.
1. Makes resources easier to manage; for example, implementing a singleton pattern is straightforward when using a Spring container.

For example, consider an operation on User, using a two-layer structure of Service and Dao.

In the absence of IoC, if the Service layer wants to use the specific implementation of the Dao layer, it needs to manually create an instance of `UserDaoImpl`, the specific implementation class of `IUserDao`, using the new keyword in `UserServiceImpl` (it cannot directly instantiate the interface class).

This approach works perfectly, but what if we encounter the following scenario during development:

Suddenly, a new requirement comes in to develop another specific implementation class for the `IUserDao` interface. Since the Service layer depends on the specific implementation of `IUserDao`, we need to modify the object we created in `UserServiceImpl`. If there is only one class referencing the specific implementation of `IUserDao`, it might be manageable, but if there are numerous references, changing the implementation of `IUserDao` will become a significant headache.

![IoC & AOP Dao-Service Illustration](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/IoC&Aop-ioc-illustration-dao-service.png)

By employing the IoC concept, we hand over the control of objects (creation, management) to the IoC container, and we can directly "request" the object from the IoC container when we need it.

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/IoC&Aop-ioc-illustration-dao.png)

### Is there a difference between IoC and DI?

IoC (Inversion of Control) is a design philosophy or a pattern. This design philosophy is **to hand over the control of manually creating objects in the program to a third party, such as the IoC container.** For the commonly used Spring framework, the IoC container is essentially a Map (key, value) that stores various objects. However, IoC is also applied in other languages and is not unique to Spring.

The most common and reasonable implementation of IoC is called Dependency Injection (DI).

Martin Fowler mentioned in an article that IoC should be renamed to DI. The original text can be found at: <https://martinfowler.com/articles/injection.html>.

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/martin-fowler-injection.png)

In essence, Fowler argues that IoC is too general and not very expressive, which can confuse many people, so it is better to use DI to accurately specify this pattern.

## AOP (Aspect-Oriented Programming)

This section will not delve into too many technical terminologies; the core purpose is to clarify the concept of AOP.

### What is AOP?

AOP (Aspect-Oriented Programming) is a continuation of OOP (Object-Oriented Programming). The two complement each other and are not opposed.

The purpose of AOP is to separate cross-cutting concerns (such as logging, transaction management, permission control, rate limiting, etc.) from the core business logic. Using dynamic proxies, bytecode manipulation, and other technologies, AOP aims to achieve code reuse and decoupling, thereby enhancing the maintainability and scalability of the code. The purpose of OOP is to encapsulate business logic according to the properties and behaviors of objects, using concepts like classes, objects, inheritance, and polymorphism to achieve code modularization (which can also ensure code reuse) and improve code readability and maintainability.

### Why is AOP called Aspect-Oriented Programming?

AOP is called Aspect-Oriented Programming because its core idea is to separate cross-cutting concerns from the core business logic, forming individual **aspects**.

![Aspect-Oriented Programming Illustration](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/aop-program-execution.jpg)

Here is a summary of key AOP terms (it's okay if you don't fully understand, you can continue reading):

- **Cross-Cutting Concerns**: Common behaviors across multiple classes or objects (such as logging, transaction management, permission control, rate limiting, etc.).
- **Aspect**: A class that encapsulates cross-cutting concerns. An aspect can define multiple advices to implement specific functionalities.
- **JoinPoint**: A specific point in time during method execution (such as method calls, exceptions being thrown, etc.).
- **Advice**: The operation to be executed at a specific join point by the aspect. There are five types of advice: Before, After, AfterReturning, AfterThrowing, and Around. The first four types of advice execute before or after the target method, while Around advice controls the execution process of the target method.
- **Pointcut**: An expression that matches which join points should be enhanced by the aspect. Pointcuts can be defined through annotations, regular expressions, logical operations, etc. For instance, `execution(* com.xyz.service..*(..))` matches classes or interfaces in the `com.xyz.service` package and its subpackages.
- **Weaving**: The process of linking aspects with target objects, i.e., applying advices to join points that meet pointcut criteria. Common weaving timing includes compile-time weaving (e.g., AspectJ) and runtime weaving (e.g., AspectJ, Spring AOP).

### What are the common types of advice in AOP?

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/aspectj-advice-types.jpg)

- **Before (Before Advice)**: Triggered before the target object's method call.
- **After (After Advice)**: Triggered after the target object's method call.
- **AfterReturning (After Returning Advice)**: Triggered after the target object's method call has completed and returned a value.
- **AfterThrowing (After Throwing Advice)**: Triggered when an exception is thrown during the execution of the target object's method. AfterReturning and AfterThrowing are mutually exclusive. If a method call succeeds without exceptions, there will be a return value; if a method throws an exception, there will not be a return value.
- **Around (Around Advice)**: Programmatically controls the execution of the target object's method. Around advice has the largest operational scope among all advice types because it can directly access the target object and the method to be executed, allowing it to perform actions before and after the target object's method call, or even omit the calling of the target object's method altogether.

### What problem does AOP solve?

OOP does not adequately handle common behaviors (such as logging, transaction management, permission control, rate limiting, etc.) that are scattered across multiple classes or objects. These behaviors are typically referred to as **cross-cutting concerns**. If we implement these behaviors repetitively in every class or object, it can lead to redundancy, complexity, and difficulty in maintaining the code.

AOP allows us to separate cross-cutting concerns (such as logging, transaction management, permission control, rate limiting, etc.) from **core business logic**, achieving separation of concerns.

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/crosscut-logic-and-businesslogic-separation%20%20%20%20%20%20.png)

For instance, in terms of logging, if we need to maintain a uniform logging format for certain methods, before using AOP, we had to write the logging logic code in each instance, which would be redundant.

```java
public CommonResponse<Object> method1() {
      // Business logic
      xxService.method1();
      // Omitted specific business processing logic
      // Logging
      ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
      HttpServletRequest request = attributes.getRequest();
      // Omitted detailed logging logic, such as gathering various information and writing to the database...
      return CommonResponse.success();
}

public CommonResponse<Object> method2() {
      // Business logic
      xxService.method2();
      // Omitted specific business processing logic
      // Logging
      ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
      HttpServletRequest request = attributes.getRequest();
      // Omitted detailed logging logic, such as gathering various information and writing to the database...
      return CommonResponse.success();
}

// ...
```

After using AOP technology, we can encapsulate the logging logic into an aspect and use pointcuts and advices to specify where logging operations should be executed.

```java
// Logging annotation
@Target({ElementType.PARAMETER, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Log {
    /**
     * Description
     */
    String description() default "";

    /**
     * Method type: INSERT, DELETE, UPDATE, OTHER
     */
    MethodType methodType() default MethodType.OTHER;
}

// Logging aspect
@Component
@Aspect
public class LogAspect {
  // Pointcut for all methods annotated with @Log
  @Pointcut("@annotation(cn.javaguide.annotation.Log)")
  public void webLog() {
  }

   /**
   * Around advice
   */
  @Around("webLog()")
  public Object doAround(ProceedingJoinPoint joinPoint) throws Throwable {
    // Omitted specific processing logic
  }

  // Omitted other code
}
```

In this way, we can achieve logging with just one line of annotation:

```java
@Log(description = "method1", methodType = MethodType.INSERT)
public CommonResponse<Object> method1() {
      // Business logic
      xxService.method1();
      // Omitted specific business processing logic
      return CommonResponse.success();
}
```

### What are the application scenarios of AOP?

- Logging: Custom logging annotation using AOP allows logging to be implemented with just one line of code.
- Performance statistics: Utilizing AOP to measure the execution time of target methods before and after their execution, facilitating optimization and analysis.
- Transaction management: The `@Transactional` annotation lets Spring handle transaction management, such as rolling back operations upon exceptions, eliminating repetitive transaction management logic. The `@Transactional` annotation is based on AOP.
- Permission control: Using AOP to check user permissions before executing target methods. If the necessary permissions exist, the target method executes; otherwise, it does not. For example, SpringSecurity allows custom permission validation with just one line of code using the `@PreAuthorize` annotation.
- Rate limiting: Using AOP to apply rate limiting algorithms and techniques before the execution of target methods.
- Cache management: Using AOP to read and update cache before and after the execution of target methods.
- …

### What are the ways to implement AOP?

Common ways to implement AOP include dynamic proxies and bytecode manipulation.

Spring AOP is based on dynamic proxies. If the target object implements an interface, Spring AOP uses **JDK Proxy** to create the proxy object. If the target object does not implement an interface, JDK Proxy cannot be employed, and Spring AOP will utilize CGLIB to generate a subclass of the target object as the proxy, as shown in the following illustration:

![Spring AOP Process](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/230ae587a322d6e4d09510161987d346.jpeg)

**Is the dynamic proxy strategy of Spring Boot and Spring the same?** Actually, they are not.

Before Spring Boot 2.0, the default was **JDK dynamic proxy**. If the target class did not implement an interface, an exception would be thrown, and developers had to explicitly configure (`spring.aop.proxy-target-class=true`) to use **CGLIB dynamic proxy** or inject an interface to resolve the issue. The auto-configuration AOP code in Spring Boot 1.5.x is as follows:

```java
@Configuration
@ConditionalOnClass({ EnableAspectJAutoProxy.class, Aspect.class, Advice.class })
@ConditionalOnProperty(prefix = "spring.aop", name = "auto", havingValue = "true", matchIfMissing = true)
public class AopAutoConfiguration {

	@Configuration
	@EnableAspectJAutoProxy(proxyTargetClass = false)
 // This configuration class only takes effect when spring.aop.proxy-target-class=false or not explicitly configured.
 // In other words, if developers do not explicitly choose a proxy method, Spring defaults to load JDK dynamic proxies.
	public static class JdkDynamicAutoProxyConfiguration {

	}

	@Configuration
	@EnableAspectJAutoProxy(proxyTargetClass = true)
 // This configuration class only takes effect when spring.aop.proxy-target-class=true.
 // If developers explicitly specify the use of CGLIB dynamic proxies, Spring will load this configuration class.
	public static class CglibAutoProxyConfiguration {

	}

}
```

Starting from Spring Boot 2.0, if users do not configure anything, **CGLIB dynamic proxy** is the default. If it is necessary to force the use of JDK dynamic proxies, an entry can be added in the configuration file: `spring.aop.proxy-target-class=false`. The auto-configuration AOP code in Spring Boot 2.0 is as follows:

```java
@Configuration
@ConditionalOnClass({ EnableAspectJAutoProxy.class, Aspect.class, Advice.class,
		AnnotatedElement.class })
@ConditionalOnProperty(prefix = "spring.aop", name = "auto", havingValue = "true", matchIfMissing = true)
public class AopAutoConfiguration {

	@Configuration
	@EnableAspectJAutoProxy(proxyTargetClass = false)
 // This configuration class only takes effect when spring.aop.proxy-target-class=false.
 // This means that if developers explicitly specify to use JDK dynamic proxies, Spring will load this configuration class.
	public static class JdkDynamicAutoProxyConfiguration {

	}

	@Configuration
	@EnableAspectJAutoProxy(proxyTargetClass = true)
 // This configuration class only takes effect when spring.aop.proxy-target-class=true or when not explicitly configured.
 // In other words, if developers do not explicitly choose a proxy method, Spring defaults to load CGLIB proxies.
	public static class CglibAutoProxyConfiguration {

	}

}
```

Of course, you can also use **AspectJ**! Spring AOP has integrated AspectJ, which can be regarded as one of the most comprehensive AOP frameworks in the Java ecosystem.

**Spring AOP is runtime enhancement, while AspectJ is compile-time enhancement.** Spring AOP is based on proxies, while AspectJ operates based on bytecode manipulation.

Spring AOP has integrated AspectJ, which can be recognized as one of the most comprehensive AOP frameworks in the Java ecosystem. AspectJ is more powerful than Spring AOP, but Spring AOP is relatively simpler.

If we have fewer aspects, the performance difference between the two is not significant. However, if there are too many aspects, it is better to choose AspectJ, as it is significantly faster than Spring AOP.

## References

- AOP in Spring Boot, is it a JDK dynamic proxy or a Cglib dynamic proxy?: <https://www.springcloud.io/post/2022-01/springboot-aop/>
- Spring Proxying Mechanisms: <https://docs.spring.io/spring-framework/reference/core/aop/proxying.html>
