---
title: Detailed Explanation of JVM Garbage Collection (Key Points)
category: Java
tag:
  - JVM
---

> Unless otherwise specified, this is referring to the HotSpot virtual machine.
>
> This article is a summary and supplement based on "In-depth Understanding of Java Virtual Machine: Advanced Features and Best Practices".
>
> Common interview questions:
>
> - How to determine if an object is dead (two methods).
> - Briefly introduce strong references, soft references, weak references, and phantom references (differences between phantom references and soft/weak references, benefits of using soft references).
> - How to determine if a constant is a deprecated constant.
> - How to determine if a class is a useless class.
> - What garbage collection algorithms are there, and what are their characteristics?
> - Why does HotSpot divide into young generation and old generation?
> - What are the common garbage collectors?
> - Introduce CMS and G1 collectors.
> - What are the differences between Minor GC and Full GC?

## Introduction

When troubleshooting various memory overflow issues, or when garbage collection becomes a bottleneck for achieving higher concurrency in the system, we need to implement necessary monitoring and adjustments for these "automated" technologies.

## Basic Structure of Heap Space

Java's automatic memory management mainly focuses on the recycling and allocation of object memory. At the same time, the core function of Java's automatic memory management is the allocation and recycling of objects in the **heap** memory.

The Java heap is the main area managed by the garbage collector, and is therefore also referred to as the **GC heap (Garbage Collected Heap)**.

From the perspective of garbage collection, since most collectors now use generational garbage collection algorithms, the Java heap is divided into several different areas, allowing us to choose appropriate garbage collection algorithms based on the characteristics of each area.

In versions prior to JDK 7 and in JDK 7, heap memory is typically divided into the following three parts:

1. Young Generation Memory
1. Old Generation
1. Permanent Generation

As shown in the diagram below, the Eden area and the two Survivor areas S0 and S1 belong to the young generation, the middle layer belongs to the old generation, and the bottom layer belongs to the permanent generation.

![Heap Memory Structure](https://oss.javaguide.cn/github/javaguide/java/jvm/hotspot-heap-structure.png)

**After JDK 8, PermGen (Permanent Generation) has been replaced by Metaspace (Meta Space), which uses direct memory**.

For a more detailed introduction to the heap space structure, you can refer back to the article [Detailed Explanation of Java Memory Areas](./memory-area.md).

## Memory Allocation and Recycling Principles

### Objects are allocated in the Eden Area first

In most cases, objects are allocated in the Eden area of the young generation. When there is not enough space in the Eden area for allocation, the virtual machine will initiate a Minor GC. Let's conduct a practical test.

Test code:

```java
public class GCTest {
  public static void main(String[] args) {
    byte[] allocation1, allocation2;
    allocation1 = new byte[30900*1024];
  }
}
```

Run it in the following way:
![](https://oss.javaguide.cn/github/javaguide/java/jvm/25178350.png)

Added parameter: `-XX:+PrintGCDetails`
![](https://oss.javaguide.cn/github/javaguide/java/jvm/run-with-PrintGCDetails.png)

Running result (the description in red is incorrect; it should correspond to the permanent generation in JDK 1.7):

![](https://oss.javaguide.cn/github/javaguide/java/jvm/28954286.jpg)

From the above image, we can see that the memory in the Eden area has almost been completely allocated (even if the program does nothing, the young generation will still use over 2000 k of memory).

What happens if we allocate memory for `allocation2`?

```java
allocation2 = new byte[900*1024];
```

![](https://oss.javaguide.cn/github/javaguide/java/jvm/28128785.jpg)

When allocating memory for `allocation2`, the memory in the Eden area has almost been fully allocated.

When there is not enough space in the Eden area for allocation, the virtual machine will initiate a Minor GC. During the GC, the virtual machine finds that `allocation1` cannot be stored in the Survivor space, so it has to use the **allocation guarantee mechanism** to transfer the young generation objects to the old generation in advance. There is enough space in the old generation to store `allocation1`, so Full GC will not occur. After executing Minor GC, if subsequent allocated objects can still exist in the Eden area, they will still be allocated memory in the Eden area. You can verify this with the following code:

```java
public class GCTest {

```
