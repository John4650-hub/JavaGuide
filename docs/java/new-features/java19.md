I don't...
title: Java 19 Overview of New Features
Category:
Tag:
- Java's new character.
I don't...

The JDK 19 is scheduled to be officially released for production on 20 September 2022, as a non-permanently supported version. However, some of the more important new features of JDK 19 are of interest.

JDK 19 has seven new features:

- [JEP 405: Record Patterns (record mode)](https://openjdk.org/jeps/405) (preview)
- [JEP 422: Linux/RISC-V Port](https://openjdk.org/jeps/422)
- [JEP 424: Foreign Function & Memory API](https://openjdk.org/jeps/424) (preview)
- [JEP 425: Virtual Threads (virtual threads)](https://openjdk.org/jeps/425) (preview)
- [JEP 426: Vector (vector) API](https://openjdk.java.net/jeps/426) (fourth incubation)
- [JEP 427:](https://openjdk.java.net/jeps/427)
- [JEP 428: Structured Concurrency (structured and distributed)](https://openjdk.org/jeps/428) (incubation)

Here's a detailed description of only 424, 425, 426, and 428 of these four new features that I think are important.

Related reading: [OpenJDK Java 19 Document](https://openjdk.org/projects/jdk/19/)

# JEP 424: Foreign Functions and Memory API (Preview)

Java programs can be interoperable with code and data other than Java while running. By calling external functions (i.e., code other than JVM) efficiently and safely to access external memory (i.e., not managed by JVM), the API enables the Java program to call and process in-house data without being as dangerous and vulnerable as JNI.

The Foreign Function and Memory API hatched in Java 17, presented by [JEP 412](https://openjdk.java.net/jeps/412). The second incubation was presented and integrated into Java 18 by [JEP 424](https://openjdk.org/jeps/424), and then into Java 19.

Before the external function and memory API:

- Java, by [`sun.misc.Unsafe`](http://hg.openjdk.java.net/jdk/jdk/file/tip/src/jdk.unsupported/share/classes/sun/misc/Unsafe.java), provides some methods of performing low-level, unsafe operations (e.g., direct access to resources in the system, autonomous management of memory resources, etc.) and `Unsafe` gives the Java language the capacity to operate in a similar way as a C-language pointer, while increasing the insecurity of the Java language. The incorrect use of the `Unsafe` class makes the error of the process more likely.
- Java 1.1 has supported the original method of call through Java Native Interface (JNI), but it is not working. JNI is too complex and cumbersome to achieve (specific steps can be referred to in this article: [Guide to JNI (Java Native Interface)](https://www.baeldung.com/jni)), which is not controlled by JVM language security mechanisms and affects the cross-platform character of the Java language. Moreover, the performance of the JNI is not good because the JNI method calls do not benefit from many common JIT optimizations (e.g., inlining). While the frameworks [JNA](https://github.com/java-native-access/jna), [JNR](https://github.com/jnr/jnr-ffi), and [JavaCP](https://github.com/bedeco/javacpp) have improved on JNI, it has not worked very well.

The introduction of the Foreign Function and Memory API is intended to address some of the pains that Java has in accessing external functions and external memory.

The Foreign Function & Memory API (FFM API) defines classes and interfaces:

- Allocation of external memory: `MemorySegment`, `MemoryAddress`, and `SegmentAllocator`;
- Operation and access to structured external memory: `MemoryLayout`, `VarHandle`;
- Controlling the distribution and release of external memory: `MemorySession`;
- Call external functions: `Linker`, `FunctionDescriptor`, and `SymbolLookup`.

The following is an example of the use of the FFM API, the code that captures the `radixsort` method handle of the C library function and uses it to sort the four strings in the Java array.

```java
// 1. Find external functions on the C library path
Linker linker = Linker.nativeLinker();
SymbolLookup stdlib = linker.defaultLookup();
MethodHandle radixSort = linker.downcallHandle(stdlib.lookup("radixsort", ...));
// 2. Allocate stack memory to store four strings
String[] javaStrings = { "mouse", "cat", "dog", "car" };
// 3. Allocate