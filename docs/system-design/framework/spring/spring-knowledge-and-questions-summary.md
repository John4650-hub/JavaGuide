---
title: Summary of Common Spring Interview Questions
category: Framework
tag:
  - Spring
---

<!-- @include: @small-advertisement.snippet.md -->

This article aims to deepen everyone's understanding of Spring through some questions, so it won't involve too much code!

Many of the questions below I didn't pay attention to during my own use of Spring, and I also consulted a lot of materials and books to fill in the gaps. There are many articles online that compile common Spring questions/interview questions, but I feel that most of them are just copies of each other, and many questions are not very good, with some answers also having issues. Therefore, I spent a week of my spare time organizing this, hoping it will be helpful to everyone.

## Spring Basics

### What is the Spring Framework?

Spring is an open-source lightweight Java development framework designed to improve developers' efficiency and the maintainability of systems.

When we refer to the Spring framework, we generally mean the Spring Framework, which is a collection of many modules. Using these modules can conveniently assist us in development. For example, Spring supports IoC (Inversion of Control) and AOP (Aspect-Oriented Programming), allows easy access to databases, integrates third-party components (email, tasks, scheduling, caching, etc.), has good support for unit testing, and supports the development of RESTful Java applications.

![](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/38ef122122de4375abcd27c3de8f60b4.png)

The core idea of Spring is to avoid reinventing the wheel, providing out-of-the-box functionality to improve development efficiency.

Spring translates to "spring" in English, indicating its goal and mission is to bring spring to Java programmers! Touching!

🤐 Just a side note: **The popularity of a language often requires a killer application, and Spring is a killer application framework in the Java ecosystem.**

The core functionalities provided by Spring are mainly IoC and AOP. When learning Spring, it is essential to understand the core ideas of IoC and AOP!

- Spring Official Website: <https://spring.io/>
- GitHub Repository: <https://github.com/spring-projects/spring-framework>

### What modules does Spring include?

**Spring 4.x version**:

![Main Modules of Spring 4.x](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/jvme0c60b4606711fc4a0b6faf03230247a.png)

**Spring 5.x version**:

![Main Modules of Spring 5.x](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/20200831175708.png)

In the Spring 5.x version, the Portlet component of the Web module has been deprecated, and the WebFlux component for asynchronous reactive processing has been added.

The dependency relationships of various Spring modules are as follows:

![Dependency Relationships of Spring Modules](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/20200902100038.png)

#### Core Container

The core module of the Spring framework, which can also be considered the foundational module, mainly provides support for IoC dependency injection. Almost all other functionalities of Spring depend on this module, as can be seen from the dependency relationship diagram of Spring modules above.

- **spring-core**: Basic core utility classes of the Spring framework.
- **spring-beans**: Provides support for the creation, configuration, and management of beans.
- **spring-context**: Provides support for internationalization, event propagation, resource loading, etc.
- **spring-expression**: Provides support for the Spring Expression Language (SpEL), which only depends on the core module and can be used independently.

#### AOP

- **spring-aspects**: This module provides support for integration with AspectJ.
- **spring-aop**: Provides an implementation of aspect-oriented programming.
- **spring-instrument**: Provides functionality for adding agents to the JVM. Specifically, it provides a weaving agent for Tomcat that can pass class files to Tomcat as if they were loaded by the class loader. It's okay if you don't understand this; the usage scenarios for this module are very limited.

#### Data Access/Integration

- **spring-jdbc**: Provides an abstraction for database access using JDBC. Different databases have their own independent APIs for database operations, while Java programs only need to interact with the JDBC API, thus shielding the impact of the database.
- **spring-tx**: Provides support for transactions.
- **spring-orm**: Provides support for ORM frameworks such as Hibernate, JPA, and iBatis.
- **spring-oxm**: Provides an abstraction layer supporting OXM (Object-to-XML Mapping), such as JAXB, Castor, XMLBeans, JiBX, and XStream.
- **spring-jms**: Message service. Since Spring Framework 4.1
