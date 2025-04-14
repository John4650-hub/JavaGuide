---
title: Summary of the Most Important JVM Parameters
category: Java
tag:
  - JVM
---

> This article is translated by JavaGuide from [https://www.baeldung.com/jvm-parameters](https://www.baeldung.com/jvm-parameters) and has been extensively supplemented.
> Documentation parameters [https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html)
> 
> JDK Version: 1.8

## 1. Overview

In this article, you will master the most commonly used JVM parameter configurations.

## 2. Heap Memory Related

> The largest portion of memory managed by the Java Virtual Machine is the Java heap, which is a memory area shared by all threads and created when the virtual machine starts. **The sole purpose of this memory area is to store object instances; almost all object instances and arrays are allocated memory here.**

![Common Configuration Parameters for Memory Areas](./pictures/内存区域常见配置参数.png)

### 2.1. Explicitly Specify Heap Memory `–Xms` and `-Xmx`

One of the most common practices related to performance is to initialize heap memory based on application requirements. If we need to specify the minimum and maximum heap size (it is recommended to specify the size explicitly), the following parameters can help you achieve this:

```bash
-Xms<heap size>[unit]
-Xmx<heap size>[unit]
```

- **heap size** indicates the specific size of memory to initialize.
- **unit** indicates the unit of memory to initialize. The units are **_“ g”_** (GB), **_“ m”_** (MB), **_“ k”_** (KB).

For example 🌰, if we want to allocate a minimum of 2 GB and a maximum of 5 GB of heap memory for the JVM, our parameters should be written as follows:

```bash
-Xms2G -Xmx5G
```

### 2.2. Explicitly Specify Young Generation Memory

According to the [Oracle official documentation](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/sizing.html), after the total available memory in the heap is configured, the second largest influencing factor is the proportion of `Young Generation` in the heap memory. By default, the minimum size of YG is 1310 _MB_, and the maximum size is _unlimited_.

There are two ways to specify the size of Young Generation memory:

**1. Specify using `-XX:NewSize` and `-XX:MaxNewSize`**

```bash
-XX:NewSize=<young size>[unit]
-XX:MaxNewSize=<young size>[unit]
```

For example 🌰, if we want to allocate a minimum of 256m and a maximum of 1024m of memory for the Young Generation, our parameters should be written as follows:

```bash
-XX:NewSize=256m
-XX:MaxNewSize=1024m
```

**2. Specify using `-Xmn<young size>[unit]`**

For example 🌰, if we want to allocate 256m of memory for the Young Generation (with NewSize and MaxNewSize set to the same), our parameters should be written as follows:

```bash
-Xmn256m
```

An important experience summary in GC tuning strategies states:

> Reserve new objects in the Young Generation, as the cost of Full GC is much higher than that of Minor GC. Therefore, it is wise to allocate objects in the Young Generation as much as possible. In actual projects, analyze the allocation of Young Generation space based on GC logs to determine if it is reasonable, and appropriately adjust the size of the Young Generation using the “-Xmn” command to minimize the situation where new objects directly enter the Old Generation.

Additionally, you can set the ratio of Old Generation to Young Generation memory using **`-XX:NewRatio=<int>`**.

For example, the following parameter sets the ratio of Young Generation to Old Generation memory to 2 (the default value). This means that the ratio of young/old is 2, with the Young Generation occupying 2/3 of the entire heap.

```plain
-XX:NewRatio=2
```

### 2.3. Explicitly Specify the Size of Permanent Generation/Metaspace

**Starting from Java 8, if we do not specify the size of Metaspace, the virtual machine will exhaust all available system memory as more classes are created (this does not happen with Permanent Generation).**

Before JDK 1.8, when Permanent Generation had not been completely removed, the size of the method area was usually adjusted using the following parameters:

```bash
-XX:PermSize=N # Initial size of the method area