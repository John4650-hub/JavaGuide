I don't...
Title: Spring Common Interview Summary
Category: Frame
Tag:

- Spring.
  I don't...

@include: @small-advertition.snippet.md-->

This article is about going through a few questions to deepen your understanding of Spring, so not too many codes!

Many of the questions that follow have not been brought to my attention in my own use of Spring, and I have consulted a lot of materials and books on an ad hoc basis. There are a lot of articles on the Internet about Spring common questions/interviews, and I feel that most of them are copy, and many of them are not very good, and some of the answers are problematic. So it took me a week to sort it out, hoping it would help you.

# Spring Foundation

# What's Spring Frame?

Spring is an open-source lightweight Java development framework designed to improve the development efficiency of developers and the maintenance of systems.

In general, the Spring framework refers to Spring Framework, which is a collection of many modules that can easily assist us in development, such as Spring Support for IoC (Inversion of Control: Controlling Reverse) and AOP (Aspect-Oriented Programming: Face-to-Face Programming), easy access to databases, easy integration of third-party components (e-mail, task, dispatch, cache, etc.), better support for unit testing, and support for the development of RESTful Java applications.

![Spring Framework](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/38ef122122de4375abcd27c3de8f60b4.png)

The core idea of Spring is not to re-engineer the wheel, to open the box and to make it more efficient.

Spring is what Spring translates, and it's what it's all about. Move!

One more word: the popular language usually requires a killer-level application, and Spring is a killer-level application framework for Java Ecology.

The core functions offered by Spring are mainly IoC and AOP. Learning Spring, you must understand the core ideas of IoC and AOP!

- Spring official: <https://spring.io/>
- GitHub address: <https://github.com/spring-projects/spring-framework>

# Spring contains what modules?

**Spring 4.x Version**:

[Spring 4.x Major Modules](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/jvme0c60b4606711fc4a0b6faf0323247a.png)

**Spring 5.x Version**:

[Spring 5.x Main Modules](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/20200831175708.png)

The Portlet component of the Web module in the Spring 5.x version has been abandoned, while the WebFlux component has been added for the stepped response.

Spring relies on the following modules:

![Spring Reliance on Modules](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/2020090210038.png)

# Core Container

The core modules of the Spring framework, or basic modules, provide support mainly for IoC-dependent injection functions. Spring, all the other functions need to rely on the module, as we can see from the rest of Spring's modules.

- **spring-core**: the basic core tool category of the Spring Framework.
- **spring-beans**: Provide support for the creation, configuration, and management of the bean.
- **spring-context**: providing support for internationalization, event dissemination, resource loading, etc.
- **spring-expression**: Provides support for the Spring Expression Language SpEL, relying only on the core module, not on other modules, which can be used separately.

# AOP

- **spring-aspects**: This module supports integration with AspectJ.
- **spring-aop**: program realization with a face-to-face orientation.
- **spring-instrument**: Additional agent (agent) for JVM is provided. Specifically, it provides Tomcat with a knitting agent that can pass to Tomcat the kind of documents that are loaded with the type loader. It does not matter if there is no understanding, and the use of this module is very limited.

# Data Access/Integration

- **spring-jdbc**: The abstract JDBC access to the database is provided. Different databases have their own independent API to operate the database, while Java programs only need to interact with JDBC API, thus blocking the impact of the database.
- **spring-tx**: Providing support for transactions.
- **spring-orm**: Provide support for Hibernate, JPA, iBatis, and other ORM frameworks.
- **spring-oxm**: Provides an abstract layer of OXM (Object-to-XML-Mapping), e.g. JAXB, Castor, XML Beans, JiBX, and XStream.
- **spring-jms**: Message service. It has also provided succession to the Spring-messaging module since Spring Platform 4.1.

# Spring Web
