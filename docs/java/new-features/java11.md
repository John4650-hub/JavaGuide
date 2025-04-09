I don't...
title: Java 11 Overview of New Features
Category:
Tag:
- Java's new character.
I don't...

**Java 11** was officially released on September 25, 2018. This is an important version! The biggest difference between Java 11 and Java 9, issued in September 2017, and Java 10, released in March 2018, is that, with respect to long-term support (Long-Term-Support), **Oracle has expressed strong support for Java 11, which will continue until September 2026. This is the first long-term version supported by Java 8 after.**

The following is the timeline officially given by Oracle for Oracle JDK support.

![Oracle JDK Support Timeline](https://oss.javaguide.cn/github/javaguide/java/new-features/4c1611fad5949edbd6e233690e9fa7.png)

**Overview (selected part)**:

(https://openjdk.java.net/jeps/321)
- [JEP 333: ZGC (scalable low-latency garbage collector)](https://openjdk.java.net/jeps/333).
- [JEP 323: Local syntax of Lambda parameters](https://openjdk.java.net/jeps/323)
- [JEP 330: Start single file source code program](https://openjdk.java.net/jeps/330)

# HTTP Client Standardization

Java 11 standardizes the Http Client API, introduced in Java 9 and updated in Java 10, and while hatching in the first two versions, the Http Client is almost completely rewritten and now fully supports the walk-in.

Moreover, in Java 11, the name of the Http Client was changed from `jdk.incubator.http` to `java.net.http`, and the API provides non-blocking requests and responsive semantics through `CompletableFuture`. It is also simple to use, as follows:

```Java
var request = HttpRequest.newBuilder()
    .uri(URI.create("https://javastack.cn"))
    .GET()
    .build();
var client = HttpClient.newHttpClient();

// Sync
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
System.out.println(response.body());

// Step aside
System.out.println(response.body());
```

# String Enhancement

Java 11 adds a series of string-processing methods:

```Java
// Judge whether the string is empty
" ...isBlank(); // true
// Remove the leading space of the string
"Java ".strip(); // "Java"
// Remove the first space of the string
" Java ".stripLeading(); // "Java"
// Remove the end space of the string
"Java ".stripTrailing(); // "Java"
// Repeat a string
"Java".repeat(3); // "JavaJavaJava"
// Returns a string that is separated by a line termination.
"A\nB\nC".lines().count(); // 3
"A\nB\nC".lines().collect(Collectors.toList());
```

# Optional Enhancement

A new `isEmpty()` method was added to determine whether the designated `Optional` object is empty.

```Java
var op = Optional.empty();
System.out.println(op.isEmpty()); // Judge whether the specified Optional object is empty
```

# ZGC (Scalable Low-Latency Garbage Collector)

**ZGC, Z Garbage Collector**, is a scalable, low-latency garbage collector.

ZGC is designed primarily to meet the following objectives:

- GC standstill not exceeding 10 ms
- It can handle a few hundred MB heaps and a few TB heaps.
- Application capacity to swallow is not reduced by more than 15% (compared to G1 recovery algorithm)
- Facilitates the introduction of new GC features and foundations for the use of colored needles and load bars
- Only Linux/x64 platforms are currently supported

ZGC **is currently in the pilot phase** and only the Linux/x64 platform is supported.

Similar to ParNew and G1 in CMS, ZGC uses tag-copy algorithms, although ZGC has made significant improvements to this algorithm.

Stop The World appears in ZGC less!

Further information can be found in: [The Exploration and Practice of the New Generation Waste Recycler ZGC](https://tech.meituan.com-2020/08/06/new-zgc-factice-in-meituan.html)

# Local Syntax of the Lambda Parameter

Starting with Java 10, local variable types are introduced to extrapolate this key feature. Type extrapolation allows the use of the keyword var as the type of local variable rather than the actual type, and the compiler extrapolates the type from the value assigned to the variable.

Java 10 has several limitations on var keywords:

- Only for local variables.
- The declaration must be initialized.
- Can't be used as a method parameter.
- Not in Lambda expressions.

Java 11 starts to allow developers to use var for parameter statements in Lambda expressions.

```Java
// Here's