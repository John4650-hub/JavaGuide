I don't...
type: most important JVM parameter summary
Category:
Tag:

- JVM.
  I don't...

> This article has been translated by JavaGuide from [https://www.baeldung.com/jvm-parameters](https://www.baeldung.com/jvm-parameters) and has been substantially supplemented.
> Document parameters [https://docs.oracle.com/javasse/8/docs/technotes/tools/unix/java.html](https://docs.oracle.com/javasse/8/docs/technotes/tools/unix/java.html)
>
> JDK Version: 1.8

# 1. General

In this article, you will have the most common JVM parameter configuration.

# 2. Core related

> Java Virtual Machine manages the largest memory, the Java Stack, which is a memory area shared by all threads and is created when the Virtual Machine starts. **The only purpose of this memory area is to store instances of objects where almost all of them, as well as arrays, allocate memory.**

!\[Current configuration parameters for memory areas\](./pictures/RAM common configuration parameters.png)

## 2.1. Visible specified memory of `-Xms` and `-Xmx`

One of the most common performance-related practices is the initialization of stack memory according to application requirements. If we need to specify the minimum and maximum stack size (recommended to show the specified size), the following parameters can help you achieve this:

```bash
[unit]
[unit]
```

- **heap size** indicates the specific size of the memory to be initialized.
- **unit** indicates the unit to initialize the memory. In units **_“g”_** (GB), **_“m”_** (MB), **_“k”_** (KB).

For example, if we want to allocate a minimum of 2 GB and a maximum of 5 GB memory size for JVM, we should write this:

```bash
-Xms2G -Xmx5G
```

## 2.2. Visible New Generation Memory

According to [Oracle Official Document](https://docs.oracle.com/javasse/8/docs/technotes/guides/vm/gctuning/sizing.html), the second most significant influencing factor is the proportion of `Young Generation` stored in a stack after the total available memory configuration has been completed. By default, the minimum size of YG is 1310 _MB_, and the maximum size is _unlimited_.

There are two ways to specify the size of the new generation memory:

**1. Adopts the designation `-XX:NewSize` and `-XX:MaxNewSize`**

```bash
-XX:NewSize=[unit]
-XX:MaxNewSize=[unit]
```

For example, if we're going to allocate to the new generation the minimum memory of 256 m and the maximum memory of 1024 m, our parameters should be as follows:

```bash
-XX:NewSize=256m
-XX:MaxNewSize=1024m
```

**2. Designation by `-Xmn<young size> [unit]`**

For example, if we're going to assign 256 m to the new generation, we should write the following parameters:

```bash
-Xmn256m
```

An important lesson learned from the GC's modulation strategy is that:

Since Full GC costs are much higher than Minor GC, it would be advisable to allocate them as far as possible to the new generation, to analyze whether the new generation's space size allocation is reasonable in the context of GC logs, and to regulate the new generation's size, as appropriate, through "-Xmn" commands, to minimize the direct entry of new objects into the old generation.

In addition, you can set the ratio between the old and the new generation memory by **-XX:NewRatio=<int>**.

The following parameter, for example, sets the ratio between the new generation and the old generation memory at 2 (default value). That means that the value of young/old is 2 and the new generation is 2/3 of the whole stack.

```plain
NewRatio=2
```

## 2.3. Visibility specifies the size of the permanent generation/metaspace

**Starting with Java 8, if we do not specify the size of Metaspace, virtual opportunities will exhaust all available systems as more classes are created (this is not the case for permanent generations).**

JDK 1.8 When a permanent generation has not been completely removed, the following parameters are usually used to adjust the size of the method area.

```bash
-XX:PermSize=N # method (permanent) Initial size
-XX:MaxPermSize=N # method (permanent generation) Maximum size, exceeding which will throw out the OutOfMemoryError anomaly: java.lang.OutOfMemoryError: PermGen
```

Garbage collection is relatively rare in the region, but it is not “per
