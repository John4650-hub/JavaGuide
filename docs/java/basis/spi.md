I don't...
Title: Java SPI Mechanism Details
Category:
Tag:

- Java Foundation
  Head:
- Meta
- Name: keywords
  Contact: Java SPI Mechanism
- Meta
- Name: description
  COMPONENT: SPI, or Service Provider Interface, literally means “the service provider interface.” It is my understanding: an interface that is made available to service providers or developers who expand the framework function. SPI separates service interfaces from specific services, decoupling service callers and service implementers, which enhances the expansiveness and maintainability of the process. Modifying or replacing the service does not require modifying the caller.
  I don't...

> This contribution is from [Kingshion](https://github.com/jjx0708). It is a very interesting thing to welcome more friends to JavaGuide's maintenance. For more information see: [JavaGuide Contribution Guide](https://javaguide.cn/javaguide/contribution-guideline.html).

Object-oriented design encourages inter-module programming based on interfaces rather than specifics, to reduce inter-module coupling, to follow the principle of reliance on inversion, and to support the principle of closure (open to extensions, closed to modifications). However, direct reliance on actuality leads to a change in the code when replacement occurs, contrary to the closure principle. In order to address this problem, SPI was created to provide a service discovery mechanism that allows for the designation of specifics in the external dynamics of the process. This is similar to the idea of control of the reverse (IoC) and transfers control over the assembly of components outside the process.

The SPI mechanism also addresses the constraints posed by the parental accreditation model in the Java-type loading system. [Paternity-assigned model](https://javaguide.cn/java/jvm/classloader.html), while ensuring the security and consistency of the core library, also limits the categories (usually achieved by third parties) in the core library or the extended library load application-type path. SPI allows core or extended library defined service interfaces to be delivered and deployed by third-party developers, and SPI service loading mechanisms to dynamically detect and load these realizations at the time of operation. For example, JDBC 4.0 and subsequent versions use SPI to autodiscover and load the database driver, and the developer simply has to place the JAR package under a class path, without `Class.forName()` to load the driver class.

# SPI Introduction

What's SPI?

SPI, or Service Provider Interface, literally means “the service provider interface,” which I understand is an interface that is available to service providers or developers who expand the framework function.

SPI separates service interfaces from specific services, decoupling service callers and service implementers, which enhances the expansiveness and maintainability of the process. Modifying or replacing the service does not require modifying the caller.

Many frameworks use Java's SPI mechanisms, such as the Spring framework, database loading drivers, log interfaces, and Dubbo expansion.

<img src="https://oss.javaguide.cn/github/javaguide/java/java/basis/spi/22e1830e0b0e4115a882751f6c417857tplv-k3u1fpffcp-zoom-1.jpeg" style="zoom:50%;"/>

What's the difference between SPI and API?

SPI has to say that the API is an interface in a broad sense and can be easily confused. Here's a picture:

[SPI VS API](https://oss.javaguide.cn/github/javaguide/java/basis/spi-vs-api.png)

Communications between general modules are done through interfaces, so we have introduced an interface between service callers and service implementers (also known as service providers).

- When the implementer provides the interface and realizes it, we can have the capacity that the implementer offers us by calling the implementer's interface, which is **API**. In this case, interfaces and realizations are placed in the package of the realizing party. The caller calls the function of the achiever through an interface without having to care about the specific details of the realization.
- When the interface exists on this side of the caller, this is **SPI**. The interface rules are determined by the caller of the interface and are then implemented by different manufacturers in accordance with this rule, thus providing services.

To give an easy-to-understand example: Company H is a technology company that has a new design of a chip, and now needs to produce it in volume, while there are several micro-manufacturing companies on the market at a time when, as long as Company H has specified the criteria for the production of the chip (which defines the interface criteria), the cooperating chip companies (service providers) deliver their own specialty chips in accordance with the criteria (providing different programs to achieve them, but giving the same results).

# Field Demonstration

SLF4J (Simple Logging Facade for Java) is a logging facade (interface) of Java, which has several practical aspects, such as Logback, Log4j, Log4j2, etc.,
