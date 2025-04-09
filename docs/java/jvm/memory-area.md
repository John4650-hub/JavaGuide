I don't...
title: Java Memory Area Detailed (focus)
Category:
Tag:

- JVM.
  I don't...

@include: @small-advertisement.snippet.md-->

> Without a special description, it's all about the HotSpot virtual machine.
>
> This paper is complemented by an in-depth understanding of Java Virtual Machine: JVM Advanced Characteristics and Best Practices.
>
> Common interview topics:
>
> - Presentation of Java Memory Area (Run-time Data Area)
> - Java Object's creation process (five steps, suggestions can be written silently and know what every step of the virtual machine does)
> - Two ways in which objects can be located (both handle and direct pointer)

Foreword

For Java programmers, there is no longer a need for a delete/free operation like the C/C++ program developer to write for every new operation. Under the virtual memory management mechanism, which is not susceptible to memory leakage and memory spills, it is precisely because Java programmers hand over memory control rights to Java Virtual Machines that, in the event of problems with memory leakage and spills, it would be a very difficult task to check wrongs without knowing how they are used.

# Runtime data range

In implementing the Java program, the Java Virtual Machine divides its managed memory into several different data areas.

JDK 1.8 is slightly different from the previous version, and we present here the two versions JDK 1.7 and JDK 1.8 as examples.

**JDK 1.7**:

[Java Runtime Data Range (JDK1.7)](https://oss.javaguide.cn/github/javaguide/java/jvm/java-runtime-data-areas-jdk1.7.png)

**JDK 1.8**:

[Java Runtime Data Range (JDK1.8)](https://oss.javaguide.cn/github/javaguide/java/jvm/java-runtime-data-areas-jdk1.8.png)

**Private:**

- Program counter.
- Virtual stack.
- Local method.

**Thread shared:**

- Heap.
- Method area.
- Direct memory (part of non-run-time data area).

The Java Virtual Machine regulation is quite liberal for run-time data areas. In the case of stacks, for example: stacks may be continuous space or may not be continuous. The size of the stack can be fixed or extended as needed while running. Virtual machine implementers can manage dumps using any recycling algorithm, even if they are not collected at all.

Program counter

The program counter is a smaller memory space that can be considered a byte code indicator for the current thread. The byte-code interpreter works by changing the value of this counter to select the next byte-code instruction that needs to be performed, and the functions of branch, cycle, jump, anomaly processing, linear restoration, etc., need to rely on this counter.

In addition, in order to recover to the right performance position after a linear transition, each route requires an independent program counter, which does not affect the other, independent storage, and we call this memory area a "lined private" memory.

From the presentation above, we know that the program counter has two main functions:

- Byte code interpreter to read instructions sequentially by changing the program counter, thus achieving process control of the code, e.g., sequence execution, selection, cycle, anomaly processing.
- In the case of multiple threads, the program counter is used to record the location of the current thread execution, so that when the thread is switched back it can know where it was last run.

Note: The program counter is the only memory area that does not appear in `OutOfMemoryError`, its life cycle is created with the creation of a thread and dies with the end of the thread.

# Java Virtual stack

Like the program counter, the Java virtual stack (the late abbreviation) is private, with the same life cycle and thread, created as the thread is created and dies as the thread dies.

In absolute terms, it is a core of JVM run-time data ranges, and all Java-based calls are made through the stacks (as will be mentioned later) with the exception of some Native methods (which also need to be matched with other run-time data areas such as the program counter).

The data to be called by the method is to be passed through the stack, and each call is to have a corresponding frame pressed into the stack, and when each method is called, a frame is ejected.

The stacks consist of a single frame, each of which has a table of local variables, an operation stack, a dynamic link, and a method to return to the address. Similar to the structure of the data, both are advanced and produced data structures, which support only the exit and entry operations.

![Stack Area](https://oss.javaguide.cn/github/javaguide/java/jvm/stack-area.png)

**The table of local variables** mainly contains data types known during the compilation period (boolean, byte, char, short, int, float, long, double) and object references (reference type,
