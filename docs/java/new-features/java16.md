I don't...
title: Java 16 Overview of New Features
Category:
Tag:

- Java's new character.
  I don't...

Java 16 was officially released on March 16, 2021. Non-permanent LTS version.

Related reading: [OpenJDK Java 16 Document](https://openjdk.java.net/projects/jdk/16/).

## JEP 338: Vector API

Vector API was originally proposed by [JEP 338](https://openjdk.java.net/jeps/338) and integrated into Java 16 as an [incubation](http://openjdk.java.net/jeps/11) API. The second round of incubation was proposed and integrated into Java 17 by [JEP 414](https://openjdk.java.net/jeps/414), while the third round of incubation was presented and integrated into Java 18 by [JEP 426](https://openjdk.java.net/jeps/426).

The incubator API provides an initial anatomy of the API to express some vector calculations that reliably compile, at the time of running, the best vector hardware commands on the CPU architecture that are supported, thus obtaining performances that are better than those calculated on the same scale, making full use of single instruction multiple data (SIMD) technology (a command that can be used on most modern CPUs). Although HotSpot supports automatic to quantitative, the convertible package is limited and vulnerable to code changes. The API will make it easy for developers to write high-performance vector algorithms that can be transplanted using Java.

In [Java 18 Overview of New Characteristics](./java18.md), I have detailed information on vector API, and no additional information is given here.

## JEP 347: Enable C++ 14 Language Features

Java 16 allows the C++ 14 language characteristics to be used in the JDK C++ source code and provides specific guidance on which features can be used in the HotSpot code.

In Java 15, the language feature used for the C++ code in JDK is limited to the C++98/03 language standard. It requires that minimum acceptable versions of the various platform compilers be updated.

## JEP 376: ZGC and Wiring Stacking

Java 16 moves ZGC linear processing from a safety point to a co-generation phase, allowing the GC safety point to be suspended in milliseconds even on large heaps. Elimination of the last delayed source in the ZGC garbage collector would greatly improve the performance and efficiency of the application.

## JEP 387: Resilient Metaspace

Since the introduction of Metaspace, according to feedback, Metaspace has often occupied excess external memory, leading to waste of memory. The elastic metaspace feature allows for faster return of unused HotSpot-type metadata memory (i.e., metaspace) to the operating system, thus reducing the space occupied by metaspace.

Moreover, the proposal also simplified the metadata code to reduce maintenance costs.

## JEP 390: Warning for Value-Based Classes

(https://xie.infoq.cn/article8304c894c4e3818d38ceb116)

As early as the Java 9 version, Java designers carried out an upgrade of the `@Deprecated` note, adding two new elements such as `since` and `forRemoval`. Of these, the `since` element is used to specify the version of the API when marked `@Deprecated` was discarded, while the `forRemoval` further clarifies the semantics of the API tag `@Deprecated`. If `forRemoval=true`, it indicates that the API will certainly be deleted in a future version, and that the developers should replace it with a new API, which is no longer susceptible to ambiguity (before Java 9, the `@Deprecated` API had a variety of possibilities, such as the risk of use, possible future compatibility errors, possible deletion in a future version, and the need for better alternatives).

A careful look at the original type of wrapper class (e.g., `java.lang.Integer`, `java.lang.Double`) shows that its constructor has been marked with `@Deprecated(since="9", forRemoval=true)` annotation, which means that its constructor will be deleted in the future and that it should not continue to be used in the proceedings such as `new Integer();` (recommended to use `Integer a = 10;` or `Integer.valueOf();`). If it continues to be used, `Integer(int)` is deprecated and marked for removal. It is also noteworthy that these wrapper types have been designated as the same values as `java.util.Optional` and `java.time.LocalDateTime`.

Second, if the value type continues to be used in the `synchronized` block, it will give warnings and even anomalies during the compilation and operation periods. It is important to note here that even if the compilation and operation periods do not give rise to warnings and anomalies, the type of value used in the `synchronized` block is not recommended as an example of self-enh
