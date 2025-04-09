I don't...
title: Java 18 Overview of New Features
Category:
Tag:

- Java's new character.
  I don't...

Java 18 was officially released on March 22, 2022, with non-permanent support.

Java 18 brings nine new features:

- [JEP 400: UTF-8 by Default (default character set UTF-8)](https://openjdk.java.net/jeps/400)
- [JEP 408: Simple Web Server](https://openjdk.java.net/jeps/408)
- [JEP 413: Code Snippets in Java API Documentation](https://openjdk.java.net/jeps/413)
- [JEP 416: Reimplement Control with Method Handles](https://openjdk.java.net/jeps/416)
- [JEP 417: Vector API](https://openjdk.java.net/jeps/417) (third incubation)
- [JEP 418: Internet-Address Resolution SPI](https://openjdk.java.net/jeps/418)
- [JEP 419: Foreign Function & Memory API](https://openjdk.java.net/jeps/419) (second incubation)
- [JEP 420: Pattern Matching for Switch](https://openjdk.java.net/jeps/420) (second preview)
- [JEP 421: Deprecate the Applet API](https://openjdk.java.net/jeps/421)

Java 17 contains 14 features, Java 16 has 17, Java 15 has 14, and Java 14 has fewer. Java 18 has significantly fewer new features than those published earlier.

Only JEPs 400, 408, 413, 416, 417, 418, and 419 are described here in detail, which I find to be more important.

Read about:

- [OpenJDK Java 18 Document](https://openjdk.java.net/projects/jdk/18/)
- [IntelliJ IDEA | Java 18 Functional Support](https://mp.weixin.qq.com/s/PocFKR9z9u7-YCZHsrA5kQ)

# JEP 400: Default Character Set UTF-8

JDK finally sets UTF-8 as the default character set.

In Java 17 and earlier versions, the default character set is determined when the Java Virtual Machine is running, depending on different operating systems, locale settings, etc., and therefore has a potential risk. For example, if you run a normal print text on Mac and the Java program on the console reaches Windows, there will be a glitch if you do not manually change the character set.

# JEP 408: Simple Web Server

After Java 18, you can start a simple static web server using the `jwebserver` command.

```bash
$jwebserver
For all uses "-b 0.0.0.0" or "-b."
Serving /cwd and subdirectories on 127.0.0.1 port 8000
URL: http://127.0.0.1:8000/
```

This server does not support CGI and Servlet, only for static files.

# JEP 413: Optimize Snippets in Java API Documentation

Before Java 18, we could use `<pre>{code...}</pre>` if we wanted to introduce a code segment into Javadoc.

```java
@code
I don't know.
It's a good idea.
```

`<pre>{@code...}</pre>` This method produces more general effects.

After Java 18, this can be done through the `@snippet` label.

```java
/**
 * The following code shows how to use
 * {@snippet:
 * if (v.isPresent())
 * System.out.println("v: " + v.get());
 */
```

`@snippet` is a more efficient and user-friendly method.

# JEP 416: Re-establish the Core of Reflection Using Method Handles

Java 18 has improved the realization logic of `java.lang.reflect.Method` and `Constructor` to make it more effective and faster. This change will not alter the associated API, which means that it is not necessary to change the reflection-related code in development to experience better reflection.

OpenJDK officially provided the results of a new and old reflective performance benchmark test.

[Reflective performance baseline test results achieved, both new and old](https://oss.javaguide.cn/github/javaguide/java/new-features/JEP416Benchmark.png)

# JEP 417: Vector API

Vector API was originally proposed by [JEP 338](https://openjdk.java.net/jeps/338) and integrated into Java 16 as an [incubation](http://openjdk.java.net/jeps/11) API. The second round of incubation was proposed and integrated into Java 17 by [JEP 414](https://openjdk.java.net/jeps/414), while the third round of incubation was
