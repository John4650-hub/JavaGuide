I don't...
Title: Java with a summary of common interviews (up)
Category:
Tag:

- Java
  Head:
- Meta
- Name: keywords
  Content: Threads and processes, combined and parallel, multiple, deadlocks, life cycle of threads
- Meta
- Name: description
  It is hoped that this will help you!
  I don't...

@include: @small-advertisement.snippet.md-->

Thread

What's a thread and process?

What's the process?

The process is a process of implementation of the process and the basic unit of the system running process, and the process is therefore dynamic. The system runs a program that is a process from creation to operation to extinction.

In Java, when we start the main function, we actually start a JVM process, and the main function's thread is a thread, also known as the main thread.

As shown in the figure below, in Windows we can clearly see the process that Windows is currently running (`.exe` file running) by viewing the task manager.

![Procedural Example Photo-Windows](https://oss.javaguide.cn/github/javaguide/java/%E8%BF%9B%E7%A8%E7%A4%BA%E4%E4%BE%8B%E5%E5%9B%BE%E7%E89%87-Windows.png)

What's the thread?

The thread is similar to the process, but it is a smaller implementing unit than the process. A process can generate multiple threads in its implementation. In contrast to the process, multiple threads share the process's **stack** and **method area** resources, each of which has its own **program counter**, **virtual machine stack**, and **local method stack**. The system has a much smaller burden than the process, and because of this, it is also known as the lightweight process.

Java is a naturally multi-threaded program, and we can look at a normal Java program with the following code.

```Java
// This post is part of our special coverage Syria Protests 2011.
public static void main(String[] args) {
    // Get Java Thread Management MX Bean
    ThreadMXBean threadMXBean = ManagementFactory.getThreadMXBean();
    // Do not need to fetch synchronized network and synchronizer information, only thread and thread stack information
    ThreadInfo[] threadInfos = threadMXBean.dumpAllThreads(false, false);
    // Walking through thread information, printing only thread ID and thread name information
    for (ThreadInfo threadInfo : threadInfos) {
        System.out.println("[" + threadInfo.getThreadId() + "] " + threadInfo.getThreadName());
    }
}
```

The above-mentioned program output is as follows (the output may be different; it does not have to focus too much on the function of each of the following threads; it is only possible to execute the main method by knowing the main):

```plain
[5] Attach Listener // Add Event
[4] Signal Dispatcher // Distribution of threads to process signals to JVM
[3] Finalizer Thread // Call Object Finalize Method
[2] Reference Handler // Clear reference thread
[1] main // Main thread, program entrance
```

As can be seen from the output above: **A Java program is run by a main thread and multiple others.**

What's the difference between a thread and an operating system?

Prior to JDK 1.2, Java threads were based on Green Threads, a user-level thread (user threads), which means that JVM simulates multi-threaded operation without relying on operating systems. Because there are limitations in the use of green threads (e.g., green threads cannot directly use functions provided by the operating system such as I/O, which can only run on a kernel, and multi-core), JDK 1.2 and later the Java thread was changed to a native thread, i.e., JVM directly uses the kernel thread (inner kernel) of the operating system to achieve the Java thread, with the movement and management of the kernel within the operating system.

In view of the fact that many readers are less aware of the differences between user and kernel threads, we would like to briefly describe:

- User threads: threads managed and operated by the user space program, operating in user space (specially for applications).
- Kernel threads: the threads that are managed and operated within the operating system, operating within the kernel space (accessible only by kernel procedures).

By the way, a brief summary of the differences and characteristics of user and kernel threads: the cost of creating and switching user threads is low, but multi-core is not available. It is costly to create and switch kernel threads and to use multi-core.

One sentence summarizes the relationship between the Java thread and the operating system: **The essence of the current Java thread is actually the operating system thread.**

Thread models are the means by which a connection between a user thread and a kernel.

1. One-to-one (one user thread corresponds to one kernel thread)
1. Many-to-one (multiple
