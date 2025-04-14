---
title: Overview of New Features in Java 19
category: Java
tag:
  - New Features in Java
---

JDK 19 is scheduled for official release for production use on September 20, 2022, as a non-long-term support version. However, there are some important new features in JDK 19 worth noting.

JDK 19 includes 7 new features:

- [JEP 405: Record Patterns (Preview)](https://openjdk.org/jeps/405)
- [JEP 422: Linux/RISC-V Port](https://openjdk.org/jeps/422)
- [JEP 424: Foreign Function & Memory API (Preview)](https://openjdk.org/jeps/424)
- [JEP 425: Virtual Threads (Preview)](https://openjdk.org/jeps/425)
- [JEP 426: Vector API (Fourth Incubation)](https://openjdk.java.net/jeps/426)
- [JEP 427: Pattern Matching for switch](https://openjdk.java.net/jeps/427)
- [JEP 428: Structured Concurrency (Incubation)](https://openjdk.org/jeps/428)

Here, I will provide a detailed introduction to the 4 features I consider most important: 424, 425, 426, and 428.

Related reading: [OpenJDK Java 19 Documentation](https://openjdk.org/projects/jdk/19/)

## JEP 424: Foreign Function and Memory API (Preview)

Java programs can interoperate with code and data outside the Java runtime through this API. By efficiently calling external functions (i.e., code outside the JVM) and safely accessing external memory (i.e., memory not managed by the JVM), this API enables Java programs to call native libraries and handle native data without the dangers and fragility associated with JNI.

The Foreign Function and Memory API was first incubated in Java 17, proposed by [JEP 412](https://openjdk.java.net/jeps/412). The second incubation was proposed by [JEP 419](https://openjdk.java.net/jeps/419) and integrated into Java 18, with the preview introduced by [JEP 424](https://openjdk.java.net/jeps/424) and integrated into Java 19.

Before the Foreign Function and Memory API:

- Java provided some methods for performing low-level, unsafe operations (such as direct access to system memory resources and manual memory management) through [`sun.misc.Unsafe`](https://hg.openjdk.java.net/jdk/jdk/file/tip/src/jdk.unsupported/share/classes/sun/misc/Unsafe.java). The `Unsafe` class gave Java the ability to manipulate memory space similarly to C language pointers, but it also increased the insecurity of the Java language, as incorrect use of the `Unsafe` class raises the probability of program errors.
- Java 1.1 supported native method calls through the Java Native Interface (JNI), but it was not user-friendly. JNI is overly complex to implement, with cumbersome steps (specific steps can be referenced in this article: [Guide to JNI (Java Native Interface)](https://www.baeldung.com/jni)), and it is not controlled by the JVM's language safety mechanisms, affecting Java's cross-platform features. Additionally, JNI's performance is poor because JNI method calls cannot benefit from many common JIT optimizations (such as inlining). Although frameworks like [JNA](https://github.com/java-native-access/jna), [JNR](https://github.com/jnr/jnr-ffi), and [JavaCPP](https://github.com/bytedeco/javacpp) have improved JNI, the results are still not ideal.

The introduction of the Foreign Function and Memory API aims to address some pain points in Java's access to external functions and external memory.

The Foreign Function & Memory API (FFM API) defines classes and interfaces for:

- Allocating external memory: `MemorySegment`, `MemoryAddress`, and `SegmentAllocator`;
- Operating on and accessing structured external memory: `MemoryLayout`, `VarHandle`;
- Controlling the allocation and release of external memory: `MemorySession`;
- Calling external functions: `Linker`, `FunctionDescriptor`, and `SymbolLookup`.

Here is an example of using the FFM API, where this code retrieves the method handle for the `radixsort` function from a C library and then uses it to sort four strings in a Java array.

```java
// 1. Look up the external function in the C library path
Linker linker = Linker.nativeLinker();
SymbolLookup stdlib = linker.defaultLookup();
MethodHandle radixSort = linker.downcallHandle(
                             stdlib.lookup("radixsort"), ...);
// 2. Allocate heap memory to store four strings
String[] javaStrings   = { "mouse", "cat", "dog", "car"