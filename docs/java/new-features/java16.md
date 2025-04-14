---
title: Overview of New Features in Java 16
category: Java
tag:
  - New Features in Java
---

Java 16 was officially released on March 16, 2021, as a non-long-term support (LTS) version.

Related reading: [OpenJDK Java 16 Documentation](https://openjdk.java.net/projects/jdk/16/) .

## JEP 338: Vector API (First Incubation)

The Vector API was initially proposed by [JEP 338](https://openjdk.java.net/jeps/338) and integrated into Java 16 as an [incubating API](http://openjdk.java.net/jeps/11). The second round of incubation was proposed by [JEP 414](https://openjdk.java.net/jeps/414) and integrated into Java 17, the third round was proposed by [JEP 417](https://openjdk.java.net/jeps/417) and integrated into Java 18, and the fourth round was proposed by [JEP 426](https://openjdk.java.net/jeps/426) and integrated into Java 19.

This incubating API provides an initial iteration of an API to express certain vector computations, which are reliably compiled at runtime into the best vector hardware instructions on supported CPU architectures, achieving better performance than equivalent scalar computations and fully utilizing Single Instruction Multiple Data (SIMD) technology (an instruction available on most modern CPUs). Although HotSpot supports automatic vectorization, the set of convertible scalar operations is limited and susceptible to code changes. This API will enable developers to easily write portable high-performance vector algorithms in Java.

I have detailed the Vector API in the [Overview of New Features in Java 18](./java18.md), so I will not provide further introduction here.

## JEP 347: Enable C++ 14 Language Features

Java 16 allows the use of C++14 language features in the C++ source code of the JDK and provides specific guidance on which features can be used in HotSpot code.

In Java 15, the language features used in the C++ code of the JDK were limited to the C++98/03 language standard. It requires updating the minimum acceptable version of various platform compilers.

## JEP 376: Concurrent Thread Stack Handling in ZGC

Java 16 moves ZGC thread stack handling from safepoints to a concurrent phase, allowing GC safepoints to pause in milliseconds even on large heaps. Eliminating the last source of latency in the ZGC garbage collector can significantly improve application performance and efficiency.

## JEP 387: Elastic Metaspace

Since the introduction of Metaspace, feedback indicated that Metaspace often consumes excessive off-heap memory, leading to memory waste. The Elastic Metaspace feature allows unused HotSpot class metadata (i.e., metaspace) memory to be returned to the operating system more quickly, thereby reducing the footprint of metaspace.

Additionally, this proposal simplifies the code for metaspace to lower maintenance costs.

## JEP 390: Warnings for Value-Based Classes

> The following introduction is excerpted from: [Practical | Analyzing Java 16 New Syntax Features](https://xie.infoq.cn/article/8304c894c4e38318d38ceb116), which is well-written and recommended for reading.

As early as Java 9, the designers of Java upgraded the `@Deprecated` annotation by adding two new elements: `since` and `forRemoval`. The `since` element specifies the version when the API marked with the `@Deprecated` annotation was deprecated, while `forRemoval` further clarifies the semantics of the API marked with the `@Deprecated` annotation. If `forRemoval=true`, it indicates that the API will definitely be removed in future versions, and developers should use the new API as a replacement, reducing ambiguity (before Java 9, APIs marked with the `@Deprecated` annotation had multiple possible meanings, such as: risk of use, potential compatibility issues in the future, possible removal in future versions, and should use better alternatives).

Upon careful observation of the wrapper classes for primitive types (e.g., `java.lang.Integer`, `java.lang.Double`), it is not difficult to find that their constructors are already marked with `@Deprecated(since="9", forRemoval = true)`, which means that their constructors will be removed in the future and should not continue to use coding styles like `new Integer();` (it is recommended to use `Integer a = 10;` or the `Integer.valueOf()` function). If continued use occurs, a compile-time warning will be generated: 'Integer(int)' is deprecated and marked for removal. Additionally, it is worth noting that these wrapper types have been designated as value types, similar to `java.util.Optional` and `java.time.LocalDateTime`.

Furthermore, if value types continue to be used in `synchronized` blocks, warnings and even exceptions will be generated
