---
title: Detailed Explanation of Java SPI Mechanism
category: Java
tag:
  - Java Basics
head:
  -   - meta
      - name: keywords
        content: Java SPI Mechanism
  -   - meta
      - name: description
        content: SPI stands for Service Provider Interface, which literally means "the interface for service providers." My understanding is that it is an interface specifically provided for service providers or developers who extend the framework's functionality. SPI separates the service interface from the specific service implementation, decoupling the service caller from the service implementer, which can enhance the program's extensibility and maintainability. Modifying or replacing the service implementation does not require changes to the caller.
---

> This article is contributed by [Kingshion](https://github.com/jjx0708). More friends are welcome to participate in the maintenance of JavaGuide, which is a very meaningful endeavor. For more details, please see: [JavaGuide Contribution Guidelines](https://javaguide.cn/javaguide/contribution-guideline.html).

Object-oriented design encourages programming between modules based on interfaces rather than concrete implementations to reduce coupling between modules, adhere to the dependency inversion principle, and support the open-closed principle (open for extension, closed for modification). However, directly depending on concrete implementations can lead to code modifications when replacing implementations, which violates the open-closed principle. To solve this problem, SPI was born, providing a service discovery mechanism that allows for dynamically specifying concrete implementations outside of the program. This is similar to the concept of Inversion of Control (IoC), which hands over the control of component assembly to outside the program.

The SPI mechanism also addresses the limitations brought by the parent delegation model in the Java class loading system. The [parent delegation model](https://javaguide.cn/java/jvm/classloader.html) ensures the security and consistency of core libraries but also restricts core or extension libraries from loading classes on the application classpath (usually implemented by third parties). SPI allows core or extension libraries to define service interfaces, while third-party developers provide and deploy implementations. The SPI service loading mechanism dynamically discovers and loads these implementations at runtime. For example, JDBC 4.0 and later versions utilize SPI to automatically discover and load database drivers; developers only need to place the driver JAR file in the classpath without explicitly loading the driver class using `Class.forName()`.

## Introduction to SPI

### What is SPI?

SPI stands for Service Provider Interface, which literally means "the interface for service providers." My understanding is that it is an interface specifically provided for service providers or developers who extend the framework's functionality.

SPI separates the service interface from the specific service implementation, decoupling the service caller from the service implementer, which can enhance the program's extensibility and maintainability. Modifying or replacing the service implementation does not require changes to the caller.

Many frameworks use Java's SPI mechanism, such as the Spring framework, database driver loading, logging interfaces, and the extension implementations of Dubbo, among others.

<img src="https://oss.javaguide.cn/github/javaguide/java/basis/spi/22e1830e0b0e4115a882751f6c417857tplv-k3u1fbpfcp-zoom-1.jpeg" style="zoom:50%;" />

### What is the difference between SPI and API?

**So what is the difference between SPI and API?**

When talking about SPI, we must mention API (Application Programming Interface). Broadly speaking, they both belong to interfaces and can easily be confused. Below is a diagram to illustrate:

![SPI VS API](https://oss.javaguide.cn/github/javaguide/java/basis/spi-vs-api.png)

Generally, modules communicate through interfaces, so we introduce an "interface" between the service caller and the service implementer (also known as the service provider).

- When the implementer provides the interface and implementation, we can call the implementer's interface to gain the capabilities provided by the implementer, which is **API**. In this case, both the interface and implementation are located in the implementer's package. The caller invokes the implementer's functionality without needing to care about the specific implementation details.
- When the interface exists on the caller's side, this is **SPI**. The interface caller determines the interface rules, and then different vendors implement this interface according to the rules to provide services.

For a simple and easy-to-understand example: Company H is a technology company that has designed a new chip and is now ready for mass production. There are several chip manufacturing companies on the market. At this point, as long as Company H specifies the production standards for the chip (defines the interface standards), these cooperating chip companies (service providers) will deliver their unique chips according to the standards (providing different implementation solutions, but yielding the same results).

## Practical Demonstration

SLF4J (Simple Logging Facade for Java) is a logging facade (interface) for Java
