I don't...
Title: JVM Waste Recovery Detail (focus)
Category:
Tag:
- JVM.
I don't...

> Without a special description, it's all about the HotSpot virtual machine.
>
> This paper is complemented by an in-depth understanding of the Java Virtual Machine: JVM Advanced Characteristics and Best Practices.
>
> Common interview topics:
>
> - How to judge the death of the subject (two methods).
> - A brief introduction to strong, soft, weak, and phantom references (distinguishing from soft and weak references, benefits from using soft references).
> - How to judge a constant as waste.
> - How to judge a class as useless.
> - What algorithms are used for garbage collection, and their respective characteristics?
> - Why should HotSpot be a new generation and an old age?
> - What are the common wastebins?
> - Introduction to CMS, G1 collector.
> - What's the difference between Minor GC and Full GC?

Foreword

When memory spills need to be checked, and when garbage collection becomes a bottleneck for higher-level systems, we need to implement the necessary monitoring and regulation of these “automated” technologies.

# Basic structure of stack space

Java's automated memory management is primarily for the recovery of object memory and the distribution of object memory. At the same time, the central function of Java Automatic Memory Management is **Stack** Allocation and Recovery of Objects in Memory.

Java Stack is the main area of garbage collector management and is therefore also known as **GC Stack**.

From the point of view of waste recovery, since the collectors are now largely using intergenerational collection algorithms, the Java heaps are divided into several different regions, so that we can select the appropriate collection algorithms for each region.

Before JDK version 7 and JDK version 7, stack memory is usually divided into three parts:

1. New Generation Memory
2. Old Generation
3. Permanent Generation

The Eden area, the two Survivor areas S0 and S1 shown in the figure below are all new generations, with the middle being the old and the lower being the permanent.

(https://oss.javaguide.cn/github/javaguide/java/jvm/hotspot-heap-structure.png)

**After JDK version 8, PermGen (permanent) has been replaced by Metaspace, which uses direct memory**.

For a more detailed description of the space structure of the stack, it is possible to look back at the article [Java](./memory-area.md).

# Memory allocation and recovery principles

# The target is assigned to Eden

In most cases, the object is distributed in the Eden area of the new generation. When the Eden area does not have enough space for distribution, the Virtual Machine will launch Minor GC once. Let's do a real test.

Test code:

```Java
This post is part of our special coverage Libya 2011.
public static void main(String[] args) {
    byte[] allocation1, allocation2;
    allocation1 = new byte[309000 * 1024];
    // I'm sorry
    // I'm sorry
}
```

Run by:
(https://oss.javaguide.cn/github/javaguide/java/jvm/25178350.png)

Added parameters: '-XX:+PrintGCDetails'
(https://oss.javaguide.cn/github/javaguide/java/jvm/run-with-PrintGCDetails.png)

Run result (red font is wrong and should correspond to JDK1.7 permanent generation):

(https://oss.javaguide.cn/github/javaguide/java/jvm/28954286.jpg)

As can be seen from the figure above, Eden's presence is almost fully distributed (even if the program doesn't do anything, the new generation will use over 2000 k memory).

What would happen if we allocated the `allocation2` memory?

```Java
allocation2 = new byte[900 * 1024];
```

(https://oss.javaguide.cn/github/javaguide/java/jvm/28128785.jpg)

By the time the `allocation2` was allocated, the Eden area had almost been allocated.

When the Eden area does not have enough space for distribution, the Virtual Machine will launch Minor GC once. The GC-time virtual machine also found that `allocation1` could not be deposited in the Survivor space, so it had to move the new generation's objects ahead of schedule to the old age, where space was sufficient for `allocation1` so that Full GC would not appear. After the Minor GC is executed, the object that is then assigned will be assigned to the Eden if it can be located. The following codes can be verified:

```Java
This post is part of our special coverage Libya 2011.

public static void main(String[] args) {
    byte[] allocation1, allocation2, allocation3, allocation4, allocation5;
    allocation1 = new byte[32000 * 1024];
    allocation2 = new byte[10000 * 1024];
    allocation3