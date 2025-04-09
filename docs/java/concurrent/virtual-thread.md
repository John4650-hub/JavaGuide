I don't...
Title: Summary of common questions on virtual threads
Category:
Tag:

- Java.
  I don't...

> Part of this paper is from [PR] in [Lorin](https://github.com/Lorin-github) (https://github.com/Snailclimb/JavaGuide/pull/2190).

The virtual thread is officially released in Java 21, which is a heavyweight update.

What's a virtual thread?

A virtual thread is a lightweight thread (LWP) achieved by JDK instead of the OS, which is controlled by the JVM. Many virtual threads share the same operating system thread, and the number of virtual threads can be much larger than the number of operating system threads.

What does the virtual thread have to do with the platform thread?

Before the introduction of the virtual thread, the `java.lang.Thread` package already supported the so-called Platform Thread, which is the thread that we used until there were virtual threads. JVM schedulers manage virtual threads through platform threads (carrier threads); a platform thread can execute different virtual threads at different times (many virtual threads are mounted on one platform thread), and when virtual threads are blocked or waiting, the platform thread can be switched to another.

The relationship between virtual threads, platform threads, and kernel threads in the system is as follows (source: [How to Use Java 19 Virtual Threads](https://mediam.com/javarevited/how-to-use-java-19-virtual-threads-c16a32bad5f7));

![Virtual Threads Relationship](https://oss.javaguide.cn/github/javaguide/java/new-features/virtual-threads-platform-threads-kernel-threads-relationship.png)

There is more to be said about the correspondence between the platform and the kernels in the system: in mainstream operating systems such as Windows and Linux, the Java routing uses a one-to-one routing model, i.e., a platform routing corresponds to a kernel in a system. Solaris is a special case where HotSpot VM supports multiple-to-one on Solaris. You could refer specifically to R's big answer: [JVM Is the thread model of a user level?](https://www.zhihu.com/question/2309668/answer/29616153).

What are the advantages and disadvantages of virtual threads?

# Advantages

- **Very lightweight**: Hundreds of virtual threads can be created in a single thread without causing excessive thread creation and context switching.
- **Simplified step-by-step programming**: Virtual threading can simplify step-by-step programming, making the code easier to understand and maintain. It makes it more like synchronized code and avoids callback hell.
- **Reduced resource costs**: Since the virtual thread is implemented by the JVM, it allows more efficient use of bottom resources such as CPU and memory. The context of the virtual thread is lighter than that of the platform thread and is therefore better able to support high-level and scalable applications.

# Disadvantages

- **Not applicable to computation-intensive tasks**: Virtual threads apply to I/O intensive tasks, but not to computation-intensive tasks, as intensive calculations always require CPU resources to support them.
- **Incompatible with certain third-party libraries**: While compatibility with existing code is taken into account in the design of virtual threads, some third-party libraries that rely on the characteristics of a platform thread may not be fully compatible with virtual threads.

# How to create virtual threads?

The following four ways of creating a virtual thread are provided:

1. Create with `Thread.startVirtualThread()`
1. Create with `Thread.ofVirtual()`
1. Create with `ThreadFactory`
1. Use `Executors.newVirtualThreadPerTaskExecutor()` to create

**1. Created using `Thread.startVirtualThread()`**

```java
public class Main {
    public static void main(String[] args) {
        CustomThread thread = new CustomThread();
        thread.startVirtualThread();
    }
}

class CustomThread extends Thread {
    @Override
    public void run() {
        System.out.println("CustomThread run");
    }
}
```

**2. Created with `Thread.ofVirtual()`**

```java
public class Main {
    public static void main(String[] args) {
        CustomThread customThread = new CustomThread();
        // Create but not start
        Thread unStarted = Thread.ofVirtual().unstarted(customThread);
        unStarted.start();
        // Create and start directly
        Thread.ofVirtual().start(customThread);
    }
}

class CustomThread extends Thread {
    @Override
    public void run() {
        System.out.println("CustomThread run");
    }
}
```

**3. Created with `ThreadFactory`**

```java
public class Main {
    public static void main(String[] args) {
        CustomThread customThread = new CustomThread();
        ThreadFactory factory = Thread.ofVirtual().factory();
        Thread thread = factory.newThread(customThread);
        thread.start();
    }
}

class CustomThread extends Thread {
    @Override
    public void run() {
        System.out.println("CustomThread run");
```
