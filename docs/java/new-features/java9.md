I don't...
title: Java 9 Overview of New Features
Category:
Tag:

- Java's new character.
  I don't...

**Java 9** was published on 21 September 2017. As a new version released three and a half years after Java 8, Java 9 brought about many significant changes, the most important of which was the introduction of the Java Platform Module System, along with others such as the `Stream` stream ...

You can download the JDK version you want on [Archived OpenJDK General-Availability Releases]. Official new identity description document address: <https://openjdk.java.net/projects/jdk/>.

**Overview (selected part)**:

- [JEP 222: Java Command Line Tool](https://openjdk.java.net/jeps/222)
- [JEP 261: Modularized System](https://openjdk.java.net/jeps/261)
- [JEP 248: G1 Becomes the Default Garbage Collector](https://openjdk.java.net/jeps/248)
- [JEP 193: Variable Handle](https://openjdk.java.net/jeps/193)
- [JEP 254: String Storage Structure Optimization](https://openjdk.java.net/jeps/254)

JShell

JShell is a new practical tool for Java 9. A real-time command line interactive tool similar to Python is provided for Java.

In JShell, you can directly enter an expression and see its execution results.

![JShell](https://oss.javaguide.cn/java-guide-blog/image-20210816083417616.png)

**What Good Did JShell Bring Us?**

1. Lower the first line of output. Java Edition "Hello World!" The threshold can increase the learning enthusiasm of newcomers.
1. The IDE is more efficient when dealing with simple logics (not to replace IDEs; IDEs are more appropriate and complementary to the validation of complex logics).
1. ...

**The JShell Code is Not the Same as the Normal Code That Can Be Compiled.**

Upon completion of the statement, JShell will be able to return the results of the execution without the need for an editor, compiler, or interpreter.
2\. JShell supports repeated statements of variables, the latter of which will cover the previous statements.
3\. JShell supports independent expressions such as normal arithmetic operations `1 + 1`.
4\. ...

# Modularized System

The modular system is part of the [Jigsaw Project](https://openjdk.java.net/projects/jigsaw/), introducing modularization development practices into the Java platform to make our code more usable!

**What's a Modular System?** Official definition:

> A uniquely named, capable group of related packages, as well as resources (such as images and XML files) and a driver.

In short, you can see a module as a set of uniquely named, reusable packages, resources, and module description documents (`module-info.java`).

Any jar file can be upgraded to a module by adding a module description file (`module-info.java`).

![Module Structure](https://oss.javaguide.cn/java-guide-blog/module-strucure.png)

After introducing the module system, JDK was reorganized into 94 modules. Java can be used through the new **[jlink](http://openjdk.java.net/jeps/282)** tool (Jlink is a new command line tool issued with Java 9. It allows developers to create their own lightweight, customized JRE for a module-based Java application, creating a custom runtime image that contains only the JDK modules on which you rely. This would greatly reduce the size of Java's environment when running.

Through the `exports` keyword, we can precisely control which packages are open to public use and which are only for internal use.

```Java
//exports All public members of the package publicly designated
exports com.my.package.name;

//exports...to limit access to members
```

For further information on the modularization of Java 9, reference can be made to the following articles:

- [Project Jigsaw: Module System Quick-Start Guide](https://openjdk.java.net/projects/jigsaw/quick-start)
- [Java 9 Modules: Part 1](https://stacktraceguru.com/java9/module-introduce)
- [Java 9 Discovery (2. Modularization System)](http://www.cnblogs.com/IcanFixIt/p/6947763.html)

# G1 Becomes the Default Garbage Collector

At Java 8, the default garbage collector was Parallel Scavenge + Parallel Old. Up to Java 9, the CMS Garbage Collector was abandoned; **the G1 (Garbage-First Garbage Collector)** became the default garbage collector.

G1 was also introduced in Java 7, and after two excellent versions, it became the default garbage collector.

# Quick Creation of Immutable Collections

Add factory methods such as `List.of()`, `Set.of()`, `Map.of()`, and `Map.ofEntries()` to create an immutable
