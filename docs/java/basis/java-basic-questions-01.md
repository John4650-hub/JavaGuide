---
title: Summary of Common Java Interview Questions (Part 1)
category: Java
tag:
  - Java Basics
head:
  -   - meta
      - name: keywords
        content: Java features, Java SE, Java EE, Java ME, Java Virtual Machine, JVM, JDK, JRE, bytecode, Java compilation and interpretation, AOT compilation, cloud-native, AOT vs JIT comparison, GraalVM, differences between Oracle JDK and OpenJDK, OpenJDK, LTS support, multithreading support, static variables, differences between member variables and local variables, wrapper type caching mechanism, autoboxing and unboxing, floating-point precision loss, BigDecimal, Java basic data types, Java identifiers and keywords, shift operators, Java comments, static methods and instance methods, method overloading and overriding, variable-length parameters, Java performance optimization
  -   - meta
      - name: description
        content: The highest quality summary of common Java knowledge points and interview questions on the internet, hoping to be helpful to you!
---

<!-- @include: @small-advertisement.snippet.md -->

## Basic Concepts and Common Knowledge

### What are the features of the Java language?

1. Simple and easy to learn (simple syntax, easy to get started);
1. Object-oriented (encapsulation, inheritance, polymorphism);
1. Platform independence (Java Virtual Machine achieves platform independence);
1. Supports multithreading (C++ does not have built-in multithreading mechanisms, so it must call the operating system's multithreading features for multithreaded program design, while Java provides multithreading support);
1. Reliability (has exception handling and automatic memory management mechanisms);
1. Security (the design of the Java language itself provides multiple security protection mechanisms such as access modifiers and restrictions on direct access to operating system resources);
1. Efficiency (through optimizations such as Just In Time compiler, the running efficiency of Java is quite good);
1. Supports network programming and is very convenient;
1. Compilation and interpretation coexist;
1. ……

> **🐛 Correction (see: [issue#544](https://github.com/Snailclimb/JavaGuide/issues/544))**: Since C++11 (in 2011), C++ introduced a multithreading library, and `std::thread` and `std::async` can be used to create threads on Windows, Linux, and macOS. Reference link: <http://www.cplusplus.com/reference/thread/thread/?kw=thread>

🌈 A little expansion:

The slogan "Write Once, Run Anywhere" is truly classic and has been passed down for many years! So much so that even today, many people still believe that cross-platform capability is the biggest advantage of the Java language. In fact, cross-platform capability is no longer the biggest selling point of Java, nor are the various new features of JDK. Currently, virtualization technology is very mature; for example, you can easily achieve cross-platform with Docker. In my opinion, the powerful ecosystem of Java is what truly stands out!

### Java SE vs Java EE

- Java SE (Java Platform, Standard Edition): The standard edition of the Java platform, which is the foundation of the Java programming language. It includes core class libraries and core components such as the virtual machine that support Java application development and execution. Java SE can be used to build desktop applications or simple server applications.
- Java EE (Java Platform, Enterprise Edition): The enterprise edition of the Java platform, built on the foundation of Java SE, includes standards and specifications that support the development and deployment of enterprise-level applications (such as Servlet, JSP, EJB, JDBC, JPA, JTA, JavaMail, JMS). Java EE can be used to build distributed, portable, robust, scalable, and secure server-side Java applications, such as web applications.

In simple terms, Java SE is the basic version of Java, while Java EE is the advanced version of Java. Java SE is more suitable for developing desktop applications or simple server applications, while Java EE is more suitable for developing complex enterprise-level applications or web applications.

In addition to Java SE and Java EE, there is also Java ME (Java Platform, Micro Edition). Java ME is a micro version of Java, mainly used for developing applications for embedded consumer electronic devices, such as mobile phones, PDAs, set-top boxes, refrigerators, air conditioners, etc. Java ME does not need to be focused on; just knowing it exists is enough, as it is no longer in use.

### JVM vs JDK vs JRE

#### JVM

The Java Virtual Machine (JVM) is a virtual machine that runs Java bytecode. JVM has specific implementations for different systems (Windows, Linux, macOS), with the goal of producing the same result using the same bytecode. The bytecode and the different system implementations of JVM are the key to Java's "compile once, run anywhere" capability.

As shown in the figure below, different programming languages (
