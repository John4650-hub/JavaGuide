---
title: Overview of Java 10 New Features
category: Java
tag:
  - Java New Features
---

**Java 10** was released on March 20, 2018, and the most notable feature is the introduction of the `var` keyword (local variable type inference). Other new features include improvements to garbage collectors, GC enhancements, performance boosts, and thread management.

**Overview (selected features)**:

- [JEP 286: Local Variable Type Inference](https://openjdk.java.net/jeps/286)
- [JEP 304: Garbage Collector Interface](https://openjdk.java.net/jeps/304)
- [JEP 307: G1 Parallel Full GC](https://openjdk.java.net/jeps/307)
- [JEP 310: Application Class-Data Sharing (Extended CDS Functionality)](https://openjdk.java.net/jeps/310)
- [JEP 317: Experimental Java-based JIT Compiler](https://openjdk.java.net/jeps/317)

## Local Variable Type Inference (var)

Due to the demand from many Java developers for local variable inference in Java, it finally arrived in Java 10, fulfilling the expectations!

Java 10 provides the `var` keyword to declare local variables.

```java
var id = 0;
var codefx = new URL("https://mp.weixin.qq.com/");
var list = new ArrayList<>();
var list = List.of(1, 2, 3);
var map = new HashMap<String, String>();
var p = Paths.of("src/test/java/Java9FeaturesTest.java");
var numbers = List.of("a", "b", "c");
for (var n : list)
    System.out.print(n + " ");
```

The `var` keyword can only be used for local variables with constructors and in for loops.

```java
var count = null; //❌ Compilation fails, cannot declare as null
var r = () -> Math.random(); //❌ Compilation fails, cannot declare as a Lambda expression
var array = {1, 2, 3}; //❌ Compilation fails, cannot declare an array
```

The `var` keyword does not change the fact that Java is a statically typed language; the compiler is responsible for inferring the type.

Additionally, the `val` keyword (a combination of `final` and `var`) already exists in Scala and Kotlin.

Related reading: [“Local Variable Type Inference in Java 10”](https://zhuanlan.zhihu.com/p/34911982).

## Garbage Collector Interface

In the early JDK structure, the components that make up the garbage collector (GC) implementation were scattered throughout the codebase. Java 10 introduces a clean garbage collector interface to separate the source code of different garbage collectors.

## G1 Parallel Full GC

Starting from Java 9, G1 became the default garbage collector. G1 is designed as a low-latency garbage collector aimed at avoiding Full GC. However, the Full GC in Java 9's G1 still used a single thread to complete the mark-sweep algorithm, which could lead to Full GC being triggered when memory could not be reclaimed.

To minimize the impact of Full GC on application pauses, starting from Java 10, G1's Full GC has been changed to a parallel mark-sweep algorithm, using the same number of parallel worker threads as young generation and mixed garbage collection, thereby reducing the occurrence of Full GC and providing better performance and greater throughput.

## Collection Enhancements

`List`, `Set`, and `Map` provide a static method `copyOf()` that returns an immutable copy of the input collection.

```java
static <E> List<E> copyOf(Collection<? extends E> coll) {
    return ImmutableCollections.listCopy(coll);
}
```

Collections created using `copyOf()` are immutable and cannot be modified (e.g., adding, removing, replacing, sorting), otherwise a `java.lang.UnsupportedOperationException` will be thrown. IDEA will also provide corresponding prompts.

![](https://oss.javaguide.cn/java-guide-blog/image-20210816154125579.png)

Additionally, new static methods have been added to `java.util.stream.Collectors` to collect elements from streams into immutable collections.

```java
var list = new ArrayList<>();
list.stream().collect(Collectors.toUnmodifiableList());
list.stream().collect(Collectors.toUnmodifiableSet());
```

## Optional Enhancements

`Optional` has added the `orElseThrow()` method to throw a specified exception when there is no value.

```java
Optional.ofNullable(cache.getIfPresent(key))
        .orElseThrow(() -> new PrestoException(NOT_FOUND, "Missing entry found for key: " + key));
```

## Application Class-Data Sharing (Extended CDS Functionality)

The Class Data Sharing (CDS) mechanism was introduced in Java 5, allowing a set of classes to be preprocessed into a shared
