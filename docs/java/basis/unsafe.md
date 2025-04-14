---
title: A Detailed Explanation of Java's Magic Class Unsafe
category: Java
tag:
  - Java Basics
---

> This article is organized and improved based on the following two excellent articles:
>
> - [Java Magic Class: Unsafe Application Analysis - Meituan Technology Team - 2019](https://tech.meituan.com/2019/02/14/talk-about-java-magic-class-unsafe.html)
> - [Java Double-Edged Sword: Detailed Explanation of Unsafe Class - Code Farmer - 2021](https://xie.infoq.cn/article/8b6ed4195e475bfb32dacc5cb)

<!-- markdownlint-disable MD024 -->

Students who have read the JUC source code will find that many concurrent utility classes call a class called `Unsafe`.

So what is this class mainly used for? What are its usage scenarios? This article will help you clarify!

## Introduction to Unsafe

`Unsafe` is a class located in the `sun.misc` package, mainly providing methods for performing low-level, unsafe operations, such as directly accessing system memory resources and managing memory resources independently. These methods play a significant role in improving Java's runtime efficiency and enhancing the underlying resource operation capabilities of the Java language. However, since the `Unsafe` class gives Java the ability to manipulate memory space similarly to C language pointers, it undoubtedly increases the risk of related pointer issues in programs. Overuse or incorrect use of the `Unsafe` class can increase the likelihood of program errors, making Java, a language known for its safety, less "safe." Therefore, the use of `Unsafe` must be approached with caution.

Additionally, the implementation of these functionalities provided by `Unsafe` relies on native methods. You can think of native methods as methods written in other programming languages that are used in Java. Native methods are marked with the **`native`** keyword, and in Java code, only the method header is declared, while the specific implementation is left to **native code**.

![](https://oss.javaguide.cn/github/javaguide/java/basis/unsafe/image-20220717115231125.png)

**Why use native methods?**

1. To utilize operating system-dependent features that are not available in Java, as Java needs to control the underlying system while achieving cross-platform compatibility, which requires the assistance of other languages.
2. To directly call some successful functionalities already completed in other languages.
3. When the program is time-sensitive or has very high performance requirements, it is necessary to use lower-level languages, such as C/C++ or even assembly.

Many concurrent utility classes in the JUC package call native methods when implementing concurrency mechanisms, breaking the boundaries of Java runtime and allowing access to certain functionalities at the operating system level. For the same native method, different operating systems may implement it in different ways, but for the user, it is transparent, and the final results will be the same.

## Creating Unsafe

The following is part of the source code for `sun.misc.Unsafe`:

```java
public final class Unsafe {
  // Singleton object
  private static final Unsafe theUnsafe;
  ......
  private Unsafe() {
  }
  @CallerSensitive
  public static Unsafe getUnsafe() {
    Class var0 = Reflection.getCallerClass();
    // Only legal when loaded by the BootstrapClassLoader
    if(!VM.isSystemDomainLoader(var0.getClassLoader())) {
      throw new SecurityException("Unsafe");
    } else {
      return theUnsafe;
    }
  }
}
```

The `Unsafe` class is implemented as a singleton, providing a static method `getUnsafe` to obtain an instance of `Unsafe`. This may seem like it can be used to get an `Unsafe` instance. However, when we directly call this static method, a `SecurityException` will be thrown:

```bash
Exception in thread "main" java.lang.SecurityException: Unsafe
 at sun.misc.Unsafe.getUnsafe(Unsafe.java:90)
 at com.cn.test.GetUnsafeTest.main(GetUnsafeTest.java:12)
```

**Why can't the `public static` method be called directly?**

This is because the `getUnsafe` method checks the caller's `classLoader` to determine whether the current class is loaded by the `Bootstrap classLoader`. If not, a `SecurityException` will be thrown. In other words, only classes loaded by the bootstrap class loader can call methods in the Unsafe class to prevent these methods from being called in untrusted code.

**Why is such cautious usage restriction placed on the Unsafe class?**

The functionalities provided by `Unsafe` are too low-level (such as directly accessing system memory resources and managing memory resources independently), and the security risks are significant. Improper use can easily lead to serious issues.

**If one wants to use the Unsafe class, how should they obtain its instance?**

Here are two feasible solutions.

1. Use reflection to obtain the already instantiated singleton object `theUnsafe` in the Unsafe class.

```java
private