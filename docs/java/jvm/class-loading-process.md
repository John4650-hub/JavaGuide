---
title: Detailed Explanation of Class Loading Process
category: Java
tag:
  - JVM
---

## Lifecycle of a Class

The entire lifecycle of a class can be simply summarized in 7 phases: Loading, Verification, Preparation, Resolution, Initialization, Using, and Unloading. Among these, Verification, Preparation, and Resolution can collectively be referred to as Linking.

The order of these 7 phases is shown in the diagram below:

![Complete Lifecycle of a Class](https://oss.javaguide.cn/github/javaguide/java/jvm/lifecycle-of-a-class.png)

## Class Loading Process

**Class files need to be loaded into the virtual machine before they can be executed and used. So how does the virtual machine load these Class files?**

The system loads Class type files mainly in three steps: **Loading -> Linking -> Initialization**. The linking process can be further divided into three steps: **Verification -> Preparation -> Resolution**.

![Class Loading Process](https://oss.javaguide.cn/github/javaguide/java/jvm/class-loading-procedure.png)

For more details, see [Java Virtual Machine Specification - 5.3. Creation and Loading](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-5.html#jvms-5.3 "Java Virtual Machine Specification - 5.3. Creation and Loading").

### Loading

The first step in the class loading process mainly involves the following 3 tasks:

1. Retrieve the binary byte stream that defines the class using its fully qualified name.
1. Convert the static storage structure represented by the byte stream into a runtime data structure for the method area.
1. Generate a `Class` object in memory that represents the class as an access point for the method area's data.

The virtual machine specification is quite flexible regarding these 3 points. For example, "getting the binary byte stream that defines the class using its fully qualified name" does not specify where to acquire it from (e.g., `ZIP`, `JAR`, `EAR`, `WAR`, network, dynamically generated at runtime through dynamic proxy technology, other file generation such as `JSP`, etc.) or how to acquire it.

This loading step is primarily completed by the **Class Loader**, which we will discuss later. There are many types of class loaders, and when we want to load a class, the specific class loader that does the loading is determined by the **parent delegation model** (however, we can also break this model).

> Class loaders and the parent delegation model are also very important concepts. This part is elaborated in the article [Detailed Explanation of Class Loaders](https://javaguide.cn/java/jvm/classloader.html "Detailed Explanation of Class Loaders"). When reading this article, it's sufficient to know that such a concept exists.

Every Java class has a reference pointing to the `ClassLoader` that loaded it. However, array classes are not created through `ClassLoader`; they are automatically created by the JVM when needed. The `ClassLoader` for an array class is consistent with that of its element type.

The loading phase for a non-array class (the action of getting the binary byte stream of the class) is the most controllable phase. At this step, we can implement and customize class loaders to control how to acquire the byte streams (by overriding the `loadClass()` method of a class loader).

Some actions in the loading phase (such as certain bytecode file format verification actions) are interleaved with the linking phase. The loading phase may not be finished when the linking phase has already begun.

### Verification

**Verification is the first step in the linking phase, aimed at ensuring that the information contained in the byte stream of the Class file meets all constraints of the Java Virtual Machine Specification, ensuring that this information will not harm the safety of the virtual machine when executed as code.**

The verification phase consumes relatively more resources in the entire class loading process, but it is very necessary as it effectively prevents the execution of malicious code. At any time, program safety is the top priority.

However, the verification phase is not always mandatory. If all the code running in the program (including custom code, third-party packages, externally loaded code, dynamically generated code, etc.) has been repeatedly used and verified, the `-Xverify:none` parameter can be considered to disable most class verification measures to shorten the class loading time of the virtual machine in production environments. However, it is noteworthy that `-Xverify:none` and `-noverify` have been marked as deprecated in JDK 13, and may be removed in future versions of the JDK.

The verification phase primarily consists of four verification stages:

1. File format verification (checks the class file format)
1. Metadata verification (checks the bytecode semantics)
1. Bytecode verification (program semantic checks)
1. Symbol reference verification (checks the correctness of the class)

![Verification Phase Diagram](https://oss.javaguide.cn/github/javaguide/java/jvm/class-loading-process-verification.png)

The file format verification phase is based on the class's binary byte stream, mainly aiming to ensure that the input byte stream can be correctly parsed and stored in the method area, and that it meets the requirements for describing Java type information. Aside from this phase, the other three verification stages are based on the storage structure of the method area and do not read or manipulate the byte stream directly.

> The method area is a logical area of the JVM's runtime data area and is a shared memory area for all threads. When the virtual machine needs to use a class, it must read and parse the Class file to obtain related information and then store this information in the method area. The method area will store the **class information, field information, method information, constants, static variables, and code caches compiled by the just-in-time compiler** that have been loaded by the virtual machine.
>
> For a detailed introduction to the method area, it is recommended to read [Detailed Explanation of Java Memory Areas](https://javaguide.cn/java/jvm/memory-area.html "Detailed Explanation of Java Memory Areas").

Symbol reference verification occurs during the resolution phase of the class loading process, specifically when the JVM converts symbolic references into direct references (the resolution phase will introduce symbolic references and direct references).

The main purpose of symbol reference verification is to ensure that the resolution phase can be executed normally. If symbol reference verification fails, the JVM will throw exceptions, such as:

- `java.lang.IllegalAccessError`: Thrown when a class attempts to access or modify a field it does not have permission to access, or calls a method it does not have permission to access.
- `java.lang.NoSuchFieldError`: Thrown when a class attempts to access or modify a specified object field, but that object no longer contains that field.
- `java.lang.NoSuchMethodError`: Thrown when a class attempts to access a specified method that does not exist.
- ...

### Preparation

**The preparation phase is the stage where memory is formally allocated for class variables and initializes them.** This memory is allocated in the method area. Notably, the following points should be noted regarding this phase:

1. At this point, memory allocation only includes class variables (Class Variables, i.e., static variables marked with the `static` keyword, which are only related to the class), and does not include instance variables. Instance variables are allocated in the Java heap alongside the object during object instantiation.
1. Conceptually, the memory used by class variables should be allocated in the **method area**. It is worth noting that before JDK 7, when HotSpot used the permanent generation to implement the method area, this implementation fully complied with this logical concept. However, from JDK 7 onward, HotSpot has moved the string constant pool, static variables, etc., which were originally placed in the permanent generation, to the heap; at this point, class variables are stored in the Java heap alongside Class objects. For further reading, see the erratum #75 in the book [Understanding Java Virtual Machine (3rd Edition)](https://github.com/fenixsoft/jvm_book/issues/75 "Understanding Java Virtual Machine (3rd Edition) Erratum #75").
1. The initial value set here is "typically" the default zero value of the data type (such as 0, 0L, null, false, etc.). For example, if we define `public static int value=111`, the initial value of the variable `value` in the preparation phase is 0 rather than 111 (it will be assigned in the initialization phase). A special case is when the `final` keyword is added to `value`, as in `public static final int value=111`; in this case, the value of `value` in the preparation phase is assigned 111.

**Zero values of primitive data types**: (Image from "Understanding Java Virtual Machine" 3rd Edition 7.3.3)

![Zero Values of Primitive Data Types](https://oss.javaguide.cn/github/javaguide/java/%E5%9F%BA%E6%9C%AC%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E7%9A%84%E9%9B%B6%E5%80%BC.png)

### Resolution

**The resolution phase is the process where the virtual machine replaces symbol references in the constant pool with direct references.** The resolution action primarily targets seven types of symbol references: class or interface, fields, class methods, interface methods, method types, method handles, and invocation qualifiers.

In Section 7.3.4 of "Understanding Java Virtual Machine" 3rd Edition, the explanations for symbol references and direct references are as follows:

![Symbol References and Direct References](https://oss.javaguide.cn/github/javaguide/java/jvm/symbol-reference-and-direct-reference.png)

For example, when invoking a method during program execution, the system needs to know the location of that method. The Java Virtual Machine prepares a method table for each class to store all methods within that class. When a method of a class needs to be called, as long as the offset of the method in the method table is known, that method can be called directly. The resolution operation converts the symbolic reference into the direct method table position of the target method within the class, enabling the method to be called.

In summary, the resolution phase is the process by which the virtual machine replaces symbolic references in the constant pool with direct references, obtaining pointers or offsets of classes, fields, or methods in memory.

### Initialization

**The initialization phase is the process of executing the initialization method `<clinit>()`, and is the last step of class loading. It is at this point that the JVM truly begins executing the Java program code (bytecode) defined in the class.**

> Note: The `<clinit>()` method is automatically generated after compilation.

The JVM will ensure the safety of calling the `<clinit>()` method in a multithreaded environment. Since the `<clinit>()` method is thread-safe with locking, initializing a class in a multithreaded environment may cause multiple threads to block, and such blocking is difficult to detect.

The JVM strictly specifies that there are only 6 situations in which a class must be initialized (a class will only be initialized when actively used):

1. When encountering the bytecode instructions `new`, `getstatic`, `putstatic`, or `invokestatic`:
   - `new`: Create an instance object of a class.
   - `getstatic`, `putstatic`: Read or set a static field of a type (except for static fields that are marked as `final` or have their results placed into the constant pool at compile time).
   - `invokestatic`: Call a static method of a class.
1. When reflecting on a class using methods from the `java.lang.reflect` package, such as `Class.forName("...")`, `newInstance()`, etc. If the class is not initialized, it needs to trigger its initialization.
1. When initializing a class, if its parent class is not initialized, it first triggers the initialization of that parent class.
1. When the JVM starts, the user defines a main class (the class containing the `main` method) to execute, and the JVM will first initialize that class.
1. `MethodHandle` and `VarHandle` can be seen as lightweight reflection mechanisms, and to use these two calls, you must first initialize the class you intend to call using `findStaticVarHandle`.
1. **"Supplementary note from [issue745](https://github.com/Snailclimb/JavaGuide/issues/745 "issue745")"**: When an interface defines a default method (marked with the `default` keyword) introduced in JDK 8, if an implementation of that interface is initialized, then that interface must be initialized before it.

## Class Unloading

> The content on unloading comes from [issue#662](https://github.com/Snailclimb/JavaGuide/issues/662 "issue#662") contributed by **[guang19](https://github.com/guang19 "guang19")**.

**Unloading a class means the Class object of that class is collected by the GC.**

To unload a class, three requirements must be met:

1. All instances of that class have been collected by the GC, meaning no instance objects of that class exist in the heap.
1. The class is not referenced anywhere else.
1. The instance of the class loader for that class has been collected by the GC.

Therefore, classes loaded by the JVM's built-in class loaders will not be unloaded within the JVM's lifecycle. However, classes loaded by our custom class loaders may be unloaded.

The key point to understand is that the JDK's built-in `BootstrapClassLoader`, `ExtClassLoader`, and `AppClassLoader` are responsible for loading classes provided by the JDK, so their instances (class loaders) will definitely not be collected. In contrast, the instances of our custom class loaders can be collected, and thus the classes loaded by our custom loaders can be unloaded.

**References**

- "Understanding Java Virtual Machine"
- "Practical Java Virtual Machine"
- Chapter 5. Loading, Linking, and Initializing - Java Virtual Machine Specification: <https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-5.html#jvms-5.4>

<!-- @include: @article-footer.snippet.md -->
