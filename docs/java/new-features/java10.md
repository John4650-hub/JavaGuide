I don't...
title: Java 10 Overview of New Features
Category:
Tag:

- Java's new character.
  I don't...

**Java 10** published on 20 March 2018, the most famous feature should be the introduction of the `var` keyword (inference of local variable type), as well as new features such as garbage collector improvement, GC improvement, performance enhancement, linear control, etc.

**Overview (selected part)**:

- [JEP 286: Local Variable Type Inference](https://openjdk.java.net/jeps/286)
- [JEP 304: Wastebin Interface](https://openjdk.java.net/jeps/304)
- [JEP 307: G1 Parallel Full GC](https://openjdk.java.net/jeps/307)
- [JEP 310: Application-Class Data Sharing (Extension of the CDS Function)](https://openjdk.java.net/jeps/310)
- [JEP 317: Experimental Java-based JIT Compiler](https://openjdk.java.net/jeps/317)

# Local Variable Type Inference (var)

Because many Java developers wanted Java to introduce local variable inference, Java came in when Java 10 was released, and it's what everyone wanted!

Java 10 provides local variables for `var` keyword statements.

```Java
var id = 0;
var codefx = new URL("https://mp.weixin.qq.com/");
var list = new ArrayList();
var list = List.of(1, 2, 3);
var map = new HashMap<String, String>();
var p = Paths.of("src/test/java/Java9Featurestest.java");
var numbers = List.of("a", "b", "c");
for (var n : list) {
    System.out.print(n + " ");
}
```

`var` keywords can only be used for local variables with a constructor and for the loop.

```Java
var count = null; // compile not valid and cannot be declared null
var r = () -> Math.random(); // ❌ Unable to be declared Lambda expression
var array = {1, 2, 3}; // ❌ unworkable, cannot declare array
```

`var` does not change the fact that Java is a statically typed language, and the compiler is responsible for inferring the type.

In addition, `val` keywords (`final var` combination) already exist in Scala and Kotlin.

Relevant reading: [Java 10 Inference of Local Variable Types for New Characters](https://zhuanlan.zhihu.com/p/34911982).

# Wastebin Interface

In the early JDK structure, the components that formed the garbage collector (GC) were dispersed in various parts of the code library. Java 10 splits the source code of different garbage collectors by introducing a pure garbage collector interface.

## Full GC

G1 has been the default garbage collector since Java 9, and G1 is designed to avoid Full GC, but Java 9 G1 Full GC still uses a one-line way to complete the tag removal algorithm, which could trigger Full GC when the recycling period is not possible.

In order to minimize the impact of the application standstill caused by Full GC, from Java 10, the Full GC of G1 was changed to a parallel marking removal algorithm, using the same number of parallel work threads as young generation and mixed recovery, thus reducing the occurrence of Full GC, leading to better performance and greater throughput.

# Gather Up and Enhance

`List`, `Set`, `Map` provides an unmodifiable copy of the static method `copyOf()`.

```Java
Status List
(b) Return Immutable Collections.listCopy;
```

Use `copyOf()` to create a collection that is non-variable and cannot be added, deleted, replaced, sorted, etc., otherwise the `java.lang.UnsupportedOperationException` is reported. IDA will have a hint.

![Image](https://oss.javaguide.cn/java-guide-blog/image-20210816151525579.png)

In addition, a static method has been added to the `java.util.stream.Collectors` to collect the elements in the stream into an immutable collection.

```Java
var list = new ArrayList();
List.stream().collect(Collectors.toUnmodifiableList());
List.stream().collect(Collectors.toUnmodifiableSet());
```

# Optional Enhancement

`Optional` added the `orElseThrow()` method to discard specified anomalies when they are not of value.

```Java
Optional.ofNullable(cache.getIfPresent(key))
.orElseThrow(() -> new PrestoException(NOT_FOUND, "Missing enterprise found for key: " + key));
```

# Application Class Data Sharing (Extension of CDS)

In Java 5, a type data-sharing mechanism (Class Data Sharing, known as CDS) was introduced to allow for a set of pre-processings to be shared as archived files, so that memory mapping can be performed while running to reduce the start time of Java, and when multiple Java Virtual Machines (JVM
