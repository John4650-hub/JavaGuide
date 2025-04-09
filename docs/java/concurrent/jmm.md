I don't...
title: JMM (Java Memory Model)
Category:
Tag:

- Java
  Head:
- Meta
- Name: keywords
  Contact: CPU Cache Model, command reordering, Java Memory Model (JMM), happens-before
- Meta
- Name: description
  In the case of Java, you can see JMM as a set of norms associated with the programming of the Java definition, which, in addition to abstracting the relationship between the thread and the primary memory, sets out the principles and norms to be followed in the transformation process from Java source code to CPU for the execution of the directive, with the main aim of simplifying multi-thread programming and enhancing the portability of the program.
  I don't...

JMM (Java Memory Model) primarily defines the visibility of a shared variable when another thread performs writing on the shared variable.

To understand the JMM (Java Memory Model), we start with the **CPU Cache Model and Command Reordering**!

From CPU Cache Model

**Why a CPU high-speed cache?** Similar to the caches used in our web-based back-office system (e.g., Redis), which were developed to address the uneven speed of program processing and access to relational databases. **The CPU Cache is intended to address the problem of the uneven speed of CPU processing and memory processing.**

We can even see **memory as a high-speed cache**, and we copy the data from the memory when the program works, which increases the processing speed as the memory is processed much faster than the memory.

Summary: **CPU Cache is used to address the mismatch between CPU processing speed and memory, while the CPU Cache is used to address the slowness of hard disk access.**

In order to better understand, I drew a simple CPU Cache diagram, as shown below.

> **Amendment (see: [issue #1848](https://github.com/Snailclimb/JavaGuide/issues/1848))**: Refinement of CPU Cache Model Mapping.

(https://oss.javaguide.cn/github/java/java/concurrent/cpu-cache.png)

Modern CPU Cache is usually divided into three layers called L1, L2, and L3 Cache. Some CPUs may have L4 Cache, but it is not very common.

**CPU Cache working method:** Copy data into CPU Cache first, and when the CPU needs to use it, you can read the data directly from CPU Cache and write back to Main Memory when the operation is complete. But there's a problem with **memory caches**! For example, if I do an i++ operation, if two threads are executed simultaneously, assuming that the two threads read from the CPU Cache that i = 1, the two threads perform the i++ operation and then write back to Main Memory after i = 2; the correct result should be i = 3.

**The problem of memory cache inconsistency can be addressed through the development of Cache Consistency Agreements (e.g., [MESI Agreement](https://zh.wikipedia.org/wiki/MESI%E5%8D%8F%E8%AE%AE)) or other means.** This Cache Consistency Agreement refers to the principles and norms to be followed when CPU high-speed caches interact with main memory. The Cache Consistency Agreements used in different CPUs are usually different.

[Cacheline Consistency Agreement](https://oss.javaguide.cn/github/javaguide/java/concurent/cpu-cache-protocol.png)

Our programs operate on the operating systems, which block the operational details of the underlying hardware and virtualize the various hardware resources. As a result, the operating system also needs to address the problem of RAM inconsistencies.

The operating system addresses this problem by defining a series of norms in the **Memory Model**. Whether Windows or Linux, they have specific memory models.

# Reorder command

After the CPU Cache Model, let's look at another, more important concept: **Directive reordering**.

In order to enhance the speed/performance of execution, the computer reorders the command when it executes the program code.

**What is the reordering of instructions?** In short, the system does not necessarily follow the order of the code you wrote.

Common commands are reordered in the following two scenarios:

- **Compilers optimized reordering**: Compilers (including JVM, JIT compilers, etc.) reorder the execution of statements without changing the syntax of the one-way program.
- **In parallel with instructions**: Modern processors use command-level parallel technology (Instruction-Level Parallelism, ILP) to execute multiple instructions. If there is no data dependence, the processor changes the order of execution of the statement corresponding to the machine command.

In addition, memory systems also have "reordering," but are not really reordering. The content expressed in JMM as primary and local memory may not be consistent, which may lead to problems in multi-thread execution.

Java Source will go through the process of optimizing the reordering of **compiler** commands in parallel with **memory system reordering**, which will eventually become an enforceable order
