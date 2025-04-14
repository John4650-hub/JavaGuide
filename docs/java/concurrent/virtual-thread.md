---
title: Summary of Common Questions about Virtual Threads
category: Java
tag:
  - Java Concurrency
---

> Some content in this article is derived from [Lorin](https://github.com/Lorin-github)'s [PR](https://github.com/Snailclimb/JavaGuide/pull/2190).

Virtual threads were officially released in Java 21, marking a significant update.

## What are Virtual Threads?

Virtual threads are lightweight threads (Lightweight Process, LWP) implemented by the JDK rather than the OS, scheduled by the JVM. Many virtual threads share the same operating system thread, and the number of virtual threads can far exceed the number of operating system threads.

## What is the relationship between Virtual Threads and Platform Threads?

Before the introduction of virtual threads, the `java.lang.Thread` package already supported what are known as platform threads. These are the threads we have been using prior to the existence of virtual threads. The JVM scheduler manages virtual threads through platform threads (carrier threads), where a platform thread can execute different virtual threads at different times (multiple virtual threads are mounted on a single platform thread). When a virtual thread is blocked or waiting, the platform thread can switch to execute another virtual thread.

The relationship diagram between virtual threads, platform threads, and system kernel threads is shown below (Image source: [How to Use Java 19 Virtual Threads](https://medium.com/javarevisited/how-to-use-java-19-virtual-threads-c16a32bad5f7)):

![Relationship between Virtual Threads, Platform Threads, and System Kernel Threads](https://oss.javaguide.cn/github/javaguide/java/new-features/virtual-threads-platform-threads-kernel-threads-relationship.png)

A bit more about the correspondence between platform threads and system kernel threads: In mainstream operating systems like Windows and Linux, Java threads adopt a one-to-one thread model, meaning one platform thread corresponds to one system kernel thread. Solaris is an exception, where the HotSpot VM supports both many-to-many and one-to-one threading. For more details, refer to R's answer: [Is the thread model in JVM user-level?](https://www.zhihu.com/question/23096638/answer/29617153).

## What are the advantages and disadvantages of Virtual Threads?

### Advantages

- **Very Lightweight**: You can create hundreds or thousands of virtual threads within a single thread without causing excessive thread creation and context switching.
- **Simplified Asynchronous Programming**: Virtual threads can simplify asynchronous programming, making the code easier to understand and maintain. They allow asynchronous code to be written more like synchronous code, avoiding callback hell.
- **Reduced Resource Overhead**: Since virtual threads are implemented by the JVM, they can utilize underlying resources like CPU and memory more efficiently. The context switching of virtual threads is lighter than that of platform threads, making them better suited for high-concurrency scenarios.

### Disadvantages

- **Not Suitable for CPU-Intensive Tasks**: Virtual threads are suitable for I/O-intensive tasks but not for CPU-intensive tasks, as intensive computation always requires CPU resources.
- **Incompatibility with Certain Third-Party Libraries**: Although virtual threads were designed with compatibility with existing code in mind, some third-party libraries that rely on platform thread characteristics may not be fully compatible with virtual threads.

## How to Create Virtual Threads?

The official documentation provides the following four ways to create virtual threads:

1. Create using `Thread.startVirtualThread()`
2. Create using `Thread.ofVirtual()`
3. Create using `ThreadFactory`
4. Create using `Executors.newVirtualThreadPerTaskExecutor()`

**1. Create using `Thread.startVirtualThread()`**

```java
public class VirtualThreadTest {
  public static void main(String[] args) {
    CustomThread customThread = new CustomThread();
    Thread.startVirtualThread(customThread);
  }
}

static class CustomThread implements Runnable {
  @Override
  public void run() {
    System.out.println("CustomThread run");
  }
}
```

**2. Create using `Thread.ofVirtual()`**

```java
public class VirtualThreadTest {
  public static void main(String[] args) {
    CustomThread customThread = new CustomThread();
    // Create without starting
    Thread unStarted = Thread.ofVirtual().unstarted(customThread);
    unStarted.start();
    // Create and start directly
    Thread.ofVirtual().start(customThread);
  }
}
static class CustomThread implements Runnable {
  @Override
  public void run() {
    System.out.println("CustomThread run");
  }
}
```

**3. Create using `ThreadFactory`**

```java
public class VirtualThreadTest {
  public static void main(String[] args) {
    CustomThread customThread = new CustomThread();
    ThreadFactory factory = Thread.ofVirtual().factory();
    Thread thread = factory.newThread(customThread);
    thread.start();
  }
}

static class CustomThread implements Runnable {
  @Override
  public void run