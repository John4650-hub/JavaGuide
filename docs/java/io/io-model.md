I don't...
title: Java IO Model Details
Category:
Tag:

- Java IO
- Java Foundation
  I don't...

The IO model is really hard to understand and requires too much computer-based knowledge. It took me a long time to write this article, so I'd like to tell you what I know. I hope my friends get something! It's hard to write this article and read the book UNIX Web Programming. Heartache?

\_ Limited personal capacity. If there is anything in the article that needs to be supplemented/improved/modified, please note in the comment section that progress is being made together! \_

Foreword

I/O has been an incomprehensible point of knowledge for many small partners, and I'll tell you what I/O understand, and I hope it will help you.

# I/O

What's I/O?

I/O (**I**nput/**O**utput) is **Input/Output**.

\*\* Let's start with the computer structure. \*\*

According to the von Neumann structure, the computer structure is divided into five major components: an operator, a controller, a storage unit, an input unit, and an output unit.

(https://oss.javaguide.cn/github/javaguide/java/io/watermark, type_ZmFuZ3poZW5naGpdGk, shadow_10, text_aHR0)

Both input equipment (e.g., keyboard) and output equipment (e.g., monitors) are external. Webcards and hard drives can be used as input or output devices.

Enter the device and enter the data into the computer, and the output device receives the data from the computer.

\*\* From a computer structure perspective, I/O describes the process of communication between computer systems and external equipment. \*\*

\*\* Let's start by reading I/O from an application perspective. \*\*

Based on the knowledge of the operational systems learned at the university: to ensure the stability and security of the operating systems, the address space of a process is divided into **user space** and **kernel space**.

Applications like the ones we usually run are in user space, and only kernel space can perform system-level resource-related operations such as document management, process communication, memory management, etc. In other words, if we want to do I/O, we have to rely on kernel capabilities.

Moreover, the user space program cannot directly access the inner kernel space.

When you want to execute I/O operations, because you do not have permission to do them, you can only initiate a system call request for help.

Therefore, if the user process is to execute the I/O operation, it must indirectly access the kernel through **system call**.

We've been most exposed in our usual development processes to **disks** and **networks**.

\*\* From an application perspective, our application initiates an I/O call (system call) to the inner core of the operating system, which is responsible for specific I/O operations. In other words, our application actually just initiated the call for the I/O operation; the specific I/O execution was done by the core of the operating system. \*\*

When the application launches I/O calls, two steps are taken:

1. Kernel waits for I/O equipment to be ready with data.
1. The kernel copies data from the kernel to the user space.

What are the common I/O models?

Under the UNIX system, I/O models have a total of five types: **synchronous blocking I/O**, **synchronous non-blocking I/O**, **I/O multi-routing**, **signal driver I/O**, and **heavy I/O**.

These are also the five I/O models that we often refer to.

# 3 Common I/O Models in Java

# BIO (Blocking I/O)

**BIO is a synchronized blocking I/O model**.

Synchronizes the blocking of I/O models, and when applications initiate read calls, they are blocked until the kernel copies the data into user space.

[Gigmagram: Deep Disassembly Tomcat & Jetty](https://oss.javaguide.cn/p3-juejin/6a9e704af49b4380bb686f0c96d33b81~tplv-k3u1fpfffcp-watermark.png)

In cases where the number of client connections is low, it is okay. But when faced with 100,000 or even million-grade connections, the traditional BIO model is powerless. So we need a more efficient I/O treatment model to deal with higher concurrency.

# NIO (Non-blocking/New I/O)

The NIO of Java was introduced in Java 1.4, corresponding to the `java.nio` package, providing abstractions such as `channel`, `selector`, and `buffer`. N of the NIO can be understood as Non-blocking, not just New. It supports a buffer-oriented, channel-based I/O operation. NIO should be used for high-load, high-throughput (network) applications.

The NIO in Java can be considered as **I/O multi-road reuse model**
