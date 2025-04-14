---
title: Summary of Common Java Concurrency Interview Questions (Part 2)
category: Java
tag:
  - Java Concurrency
head:
  -   - meta
      - name: keywords
        content: multithreading, deadlock, synchronized, ReentrantLock, volatile, ThreadLocal, thread pool, CAS, AQS
  -   - meta
      - name: description
        content: Summary of common knowledge points and interview questions in Java concurrency (with detailed answers).
---

<!-- @include: @article-header.snippet.md -->

## ⭐️ JMM (Java Memory Model)

There are many important questions related to JMM (Java Memory Model), so I have dedicated a separate article to summarize the knowledge points and questions related to JMM: [Detailed Explanation of JMM (Java Memory Model)](./jmm.md).

## ⭐️ volatile Keyword

### How to Ensure Variable Visibility?

In Java, the `volatile` keyword can ensure the visibility of a variable. If we declare a variable as **`volatile`**, it indicates to the JVM that this variable is shared and unstable, and every time it is used, it will be read from the main memory.

![JMM (Java Memory Model)](https://oss.javaguide.cn/github/javaguide/java/concurrent/jmm.png)

![JMM (Java Memory Model) Forces Reading from Main Memory](https://oss.javaguide.cn/github/javaguide/java/concurrent/jmm2.png)

The `volatile` keyword is not unique to the Java language; it also exists in C. Its original meaning is to disable CPU caching. If we modify a variable with `volatile`, it indicates to the compiler that this variable is shared and unstable, and every time it is used, it will be read from the main memory.

The `volatile` keyword can ensure data visibility but cannot guarantee data atomicity. The `synchronized` keyword can guarantee both.

### How to Prevent Instruction Reordering?

**In Java, the `volatile` keyword not only ensures variable visibility but also plays an important role in preventing JVM instruction reordering.** If we declare a variable as **`volatile`**, when performing read and write operations on this variable, specific **memory barriers** will be inserted to prevent instruction reordering.

In Java, the `Unsafe` class provides three out-of-the-box methods related to memory barriers, which shield the differences at the operating system level:

```java
public native void loadFence();
public native void storeFence();
public native void fullFence();
```

Theoretically, you can achieve the same effect as `volatile` in preventing reordering using these three methods, but it would be more complicated.

Below, I will explain the effect of the `volatile` keyword in preventing instruction reordering using a common interview question.

Interviewers often ask: "Are you familiar with the singleton pattern? Please write it out for me! Explain the principle of implementing the singleton pattern using double-checked locking!"

**Double-Checked Locking Implementation of Singleton (Thread-Safe)**:

```java
public class Singleton {

    private volatile static Singleton uniqueInstance;

    private Singleton() {
    }

    public static Singleton getUniqueInstance() {
        // First check if the object has already been instantiated; only enter the locking code if it hasn't been instantiated
        if (uniqueInstance == null) {
            // Lock the class object
            synchronized (Singleton.class) {
                if (uniqueInstance == null) {
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }
}
```

It is necessary to use the `volatile` keyword to modify `uniqueInstance`. The line `uniqueInstance = new Singleton();` is actually executed in three steps:

1. Allocate memory space for `uniqueInstance`.
1. Initialize `uniqueInstance`.
1. Point `uniqueInstance` to the allocated memory address.

However, due to the JVM's instruction reordering characteristics, the execution order may become 1->3->2. Instruction reordering does not pose a problem in a single-threaded environment, but in a multi-threaded environment, it can lead to one thread obtaining an instance that has not yet been initialized. For example, if thread T1 executes steps 1 and 3, and then thread T2 calls `getUniqueInstance()` and finds `uniqueInstance` is not null, it will return `uniqueInstance`, but at this point, `uniqueInstance` has not yet been initialized.

### Can volatile Ensure Atomicity?

**The `volatile` keyword can ensure variable visibility but cannot guarantee that operations on the variable are atomic.**

We can prove this with the following code:

```java
/**
 * Search for "JavaGuide" on WeChat to receive a free personal original Java interview manual
 *
 * @author Guide
 * @date 2022/08/03 13:40
 **/
public class VolatileAtomicityDemo {
    public volatile static int inc = 0;

    public void increase() {
```
