I don't...
Title: Java Basic Common Interview Summary (above)
Category:
Tag:

- Java Foundation.
  Head:
- Meta.
- Name: keywords
  Java feature, Java SE, Java EE, Java ME, Java Virtual Machine, JVM, JDK, JRE, byte code, Java compiler and interpreter, AOT compiler, cloud origin, AOT versus JIT, GraalVM, Oracle JDK and OpenJDK, OpenJDK, LTS support, multi-line support, static variables, differences between member and local variables, cache mechanisms for packaging type, automatic loading and unpacking, loss of floating point accuracy, BigDecimal, Java Basic data type, Java identifier and keywords, migration calculator, Java comment, static method and example method, rewriting, variable parameters, Java sexuality optimization
- Meta.
- Name: description
  It is hoped that the most high-quality Java basic knowledge points and interview summaries available online will help you!
  I don't...

@include: @small-advertition.snippet.md-->

# Basic concepts and common sense

What are the characteristics of the Java language?

1. Easy to learn (simple grammar, easy to use);
1. Object-oriented (encapsulation, inheritance, multi-state);
1. The platform's irrelevance (Java virtual machine to the platform's irrelevance);
1. Supporting multi-threading (C++ languages do not have built-in multi-threading mechanisms, and the multi-threading system multi-threading feature must be called for multi-thread programming, while the Java language provides multi-thread support);
1. Reliability (extraordinary treatment and automated memory management mechanisms are in place);
1. Safety (the Java language itself is designed to provide multiple security protection mechanisms such as access rights amplifiers, restricted program direct access to operating system resources);
1. Efficiency (through technological optimization such as the Just In Time compiler, the Java language is still operating very efficiently);
1. Supporting and facilitating web programming;
1. Compile and interpret;
   10....

> \*\* Amendment (see: [issue #544](https://github.com/Snailcrimb/JavaGuide/issues/544)): C++ has been introduced since the beginning of C++ (2011), and `std::thread` and `std::async` can be used to create a thread in Windows, Linux, MacOS. Reference link: <http://www.cplusplus.com/reference/thread/thread/?kw=thread>

Zoom in.

"Write Once, Run Anywhere" is a slogan that's been in circulation for years! So, to this day, a lot of people still feel that cross-platform is the greatest advantage of the Java language. In fact, cross-platforms are not the biggest selling point for Java, nor are the new JDK features. Now, virtualization is so mature that you can get across the platform through Docker. In my opinion, Java is a powerful ecology!

# Java SE vs Java EE

- Java SE (Java Platform, Standard Edition): The Java platform standard version, the basis of the Java programming language, which contains core modules such as the core library and virtual machines that support the development and operation of Java applications. Java SE can be used to build desktop applications or simple server applications.
- Java EE (Java Platform, Enterprise Edition): The Java platform enterprise version, based on Java SE, contains standards and norms to support the development and deployment of enterprise-level applications (e.g., Servlet, JSP, EJB, JDBC, JPA, JTA, JavaMail, JMS). Java EE can be used to build distributed, portable, robust, scalable, and secure service-end Java applications, such as Web applications.

Simply put, Java SE is the basic version of Java, and Java EE is the advanced version of Java. Java SE is better suited to develop desktop applications or simple server applications, and Java EE is better suited to develop complex enterprise or Web applications.

In addition to Java SE and Java EE, there is Java ME (Java Platform, Micro Edition). Java ME is a mini-version of Java, which is used mainly to develop applications for embedded consumer electronic devices, such as mobile phones, PDAs, set-top boxes, refrigerators, air conditioners, etc. Java ME doesn't need to be focused on this. It's good to know that this thing is not needed anymore.

# JVM vs JDK vs JRE

JVM

Java Virtual Machine, JVM is a virtual machine that runs Java bytecode. JVM has specific implementations for different systems (Windows, Linux, MacOS) aimed at using the same bytecode, and they all give the same results. The bytecode and the JVM implementation of different systems is the key to the Java language, "one-time compilation, one-time running."

As shown in the figure below, different programming languages (Java, Groovy, Kotlin, JRuby, Clojure ...) are translated into `.class` files through their respective compilers and eventually run on different platforms (Windows, Mac, Linux) through JVM.

(https://oss.javaguide.cn/github/javaguide/java/basis/java-virtual-machine
