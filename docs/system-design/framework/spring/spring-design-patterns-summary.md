---
title: Detailed Explanation of Design Patterns in Spring
category: Framework
tag:
  - Spring
---

"What design patterns are used in JDK? What design patterns are used in Spring?" These two questions are quite common in interviews.

I searched online for explanations of design patterns in Spring, and most of them are quite uniform and outdated. Therefore, I spent a few days summarizing my own understanding.

Due to my limited personal ability, if there are any errors in the text, please feel free to point them out. Additionally, due to the limited length of the article, I will only briefly touch on the design patterns and some source code interpretations. The main purpose of this article is to review the design patterns in Spring.

## Inversion of Control (IoC) and Dependency Injection (DI)

**IoC (Inversion of Control)** is a very important concept in Spring. It is not a technology but a design philosophy for decoupling. The main purpose of IoC is to achieve decoupling between objects with dependencies through a "third party" (the IoC container in Spring), which reduces the coupling between code.

**IoC is a principle, not a pattern. The following patterns (but not limited to) implement the IoC principle.**

![ioc-patterns](https://oss.javaguide.cn/github/javaguide/ioc-patterns.png)

**The Spring IoC container acts like a factory. When we need to create an object, we only need to configure the configuration file/annotations and do not need to consider how the object is created.** The IoC container is responsible for creating objects, connecting them, configuring these objects, and managing the entire lifecycle of these objects until they are completely destroyed.

In a real project, if a Service class has hundreds or even thousands of classes as its underlying components, when we need to instantiate this Service, we might have to figure out the constructors of all the underlying classes each time, which can be quite frustrating. By using IoC, you only need to configure it and reference it where needed, greatly increasing the maintainability of the project and reducing development difficulty.

> For a better understanding of Spring IoC, I recommend reading this answer on Zhihu: <https://www.zhihu.com/question/23277575/answer/169698662>, which is very good.

**How to understand Inversion of Control?** For example: "Object a depends on object b. When object a needs to use object b, it must create it itself. However, when the system introduces the IoC container, the direct connection between object a and object b is lost. At this point, when object a needs to use object b, we can specify the IoC container to create an object b and inject it into object a." The process of object a obtaining the dependent object b changes from an active behavior to a passive behavior, and the control is inverted, which is the origin of the name Inversion of Control.

**DI (Dependency Injection) is a design pattern that implements Inversion of Control. Dependency injection means passing instance variables into an object.**

## Factory Design Pattern

Spring uses the factory pattern to create bean objects through `BeanFactory` or `ApplicationContext`.

**Comparison of the two:**

- `BeanFactory`: Lazy injection (injected only when a certain bean is used), consumes less memory compared to `ApplicationContext`, and starts the program faster.
- `ApplicationContext`: Creates all beans at once when the container starts, regardless of whether they are used or not. `BeanFactory` only provides the most basic dependency injection support, while `ApplicationContext` extends `BeanFactory`, offering additional features, so developers generally use `ApplicationContext` more.

Three implementations of `ApplicationContext`:

1. `ClassPathXmlApplication`: Treats the context file as a classpath resource.
1. `FileSystemXmlApplication`: Loads context definition information from an XML file in the file system.
1. `XmlWebApplicationContext`: Loads context definition information from an XML file in the web system.

Example:

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.FileSystemXmlApplicationContext;

public class App {
  public static void main(String[] args) {
    ApplicationContext context = new FileSystemXmlApplicationContext(
        "C:/work/IOC Containers/springframework.applicationcontext/src/main/resources/bean-factory-config.xml");

    HelloApplicationContext obj = (HelloApplicationContext) context.getBean("helloApplicationContext");
    obj.getMsg();
  }
}
```

## Singleton Design Pattern

In our system, there are some objects that we only need one of, such as: thread pools, caches, dialog boxes, registries, log objects, and objects acting as drivers for printers, graphics cards, etc. In fact, this type of object should only have one instance; creating multiple instances may lead to issues such as abnormal program behavior, excessive resource usage, or inconsistent results.

**Benefits of using the singleton pattern:**

- For
