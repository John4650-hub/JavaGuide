---
title: High-Quality Open Source System Design Projects in Java
category: Open Source Projects
icon: systemdesign
---

## Basic Framework

### Web Frameworks

- [Spring Boot](https://github.com/spring-projects/spring-boot "spring-boot"): Spring Boot makes it easy to create standalone, production-grade Spring-based applications, with an embedded web server that allows you to run your project like a regular Java program. Additionally, most Spring Boot projects require minimal configuration, unlike the heavy configuration of Spring.
- [SOFABoot](https://github.com/sofastack/sofa-boot): SOFABoot is based on Spring Boot but adds capabilities such as Readiness Check, class isolation, and log space isolation. It also provides: SOFARPC (RPC framework), SOFABolt (Netty-based remote communication framework), SOFARegistry (registry center)... For more details, please refer to: [SOFAStack](https://github.com/sofastack).
- [Solon](https://gitee.com/opensolon/solon): A domestic Java enterprise application development framework aimed at all scenarios.
- [Javalin](https://github.com/tipsy/javalin): A lightweight web framework that supports both Java and Kotlin, used by companies like Microsoft, Red Hat, and Uber.
- [Play Framework](https://github.com/playframework/playframework): A high-speed web framework for Java and Scala.
- [Blade](https://github.com/lets-blade/blade): A web framework that pursues simplicity and efficiency, based on Java 8 + Netty 4.

### Microservices/Cloud Native

- [Armeria](https://github.com/line/armeria): A microservices framework suitable for any situation. You can build any type of microservice using your preferred technology, including [gRPC](https://grpc.io/), [Thrift](https://thrift.apache.org/), [Kotlin](https://kotlinlang.org/), [Retrofit](https://square.github.io/retrofit/), [Reactive Streams](https://www.reactive-streams.org/), [Spring Boot](https://spring.io/projects/spring-boot), and [Dropwizard](https://www.dropwizard.io/).
- [Quarkus](https://github.com/quarkusio/quarkus): A cloud-native and container-first framework for writing Java applications.
- [Helidon](https://github.com/helidon-io/helidon): A set of Java libraries for writing microservices, supporting both Helidon MP and Helidon SE programming models.

### API Documentation

- [Swagger](https://swagger.io/): A mainstream RESTful API documentation tool that provides a set of tools and specifications to help developers create and maintain readable, user-friendly, and interactive API documentation.
- [Knife4j](https://doc.xiaominfo.com/): An enhanced solution that integrates Swagger2 and OpenAPI3.

### Bean Mapping

- [MapStruct](https://github.com/mapstruct/mapstruct) (recommended): A Java annotation processor that meets the JSR269 specification, used to generate type-safe and high-performance mappings for Java Beans. It generates get/set code at compile time without reflection, avoiding additional performance overhead.
- [JMapper](https://github.com/jmapper-framework/jmapper-core): A high-performance and easy-to-use bean mapping framework.

### Others

- [Guice](https://github.com/google/guice): A lightweight dependency injection framework open-sourced by Google, akin to a simplified version of Spring Boot. It is very practical in cases where our project only needs dependency injection without features like AOP.
- [Spring Batch](https://github.com/spring-projects/spring-batch): A lightweight yet comprehensive batch processing framework primarily used for batch processing scenarios, such as reading large records from databases, files, or queues. However, it is important to note that Spring Batch is not a scheduling framework. There are many excellent enterprise scheduling frameworks in both commercial and open-source domains, such as Quartz, XXL-JOB, and Elastic-Job. It is designed to work with schedulers rather than replace them.

## Authentication and Authorization

### Permission Authentication

- [Sa-Token](https://github.com/dromara/sa-token): A lightweight Java permission authentication framework. It supports authentication and authorization, single sign-on, user kick-off, automatic renewal, and more. Compared to Spring Security and Shiro, Sa-Token has more built-in out-of-the-box features and is easier to use.
- [Spring Security](https://github.com/spring-projects/spring-security): The official security framework from Spring, used for authentication, authorization, encryption, and session management, and is currently the most widely used Java security framework.
- [Shiro](https://github.com/apache/shiro): A Java security framework similar to Spring Security but simpler to use.

### Third-Party Login

- \[WxJava\](https://
