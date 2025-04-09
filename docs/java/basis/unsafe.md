I don't...
Title: Java Unsafe
Category:
Tag:

- Java Foundation.
  I don't...

This post is part of our special coverage of Global Development 2011.

> - [Java Magic: Unsafe Application Parsing - Mission Technical Team - 2019](https://tech.meituan.com/2019/02/14/talk-about-java-magic-class-unsafe.html)
> - [Java 2-edged sword: Unsafe - Ensorption - 2021]

- markdownlint-disable MD024--

Students who have read the JUC source code will surely find that many of the concoction tools have called a class called `Unsafe`.

So what's this kind of thing? What use is it? This article will clear you up!

Unsafe Introduction

`Unsafe` is a category under the `sun.misc` package, which provides, inter alia, methods for low-level, unsafe operations, such as direct access to the system and the autonomous management of memory resources, which have been instrumental in improving the efficiency of Java's operations and enhancing the operational capacity of Java's bottom resources. However, the fact that the `Unsafe` class gives Java the ability to operate memory space similar to the C pointer undoubtedly increases the risk of the program having problems with the pointer. The excessive and incorrect use of the `Unsafe` category in the proceedings would increase the probability of errors in the proceedings, making Java a safe language no longer “safe” and therefore the use of `Unsafe` must be considered with caution.

In addition, `Unsafe` provides these functions that depend on local methods (Native Method). You can see local methods as the way Java uses other programming languages. Local methods use the `native` keyword, with Java code only declaring the header of the method, and actual realization given to local code.

![Image](https://oss.javaguide.cn/github/javaguide/java/basis/unsafe/image-2022071711511225.png)

**Why use local methods?**

1. There is a need to use the operational system-dependent features not available in Java, which needs to function in other languages in order to achieve bottom control while crossing the platform.
1. For some of the current successful performances of other languages, Java may be called directly.
1. When procedures are time-sensitive or very demanding, it is necessary to use lower-level languages, such as C/C++ or even compilations.

When many of the JUC packages combine tools to achieve co-generation mechanisms, local methods are used to break the boundaries of Java's running time and to access certain functions at the bottom of the operating system. For the same local method, different operating systems may be achieved in different ways, but they are transparent to users and will eventually achieve the same results.

# Unsafe Create

`sun.misc.Unsafe`, part of the source code, is as follows:

```java
// Single Object
public static Unsafe getUnsafe() {
    Class<?> var0 = Reflection.getCallerClass();
    // legal only when guided class loader `BootstrapClassLoader`
    if (!VM.isSystemDomainLoader(var0.getClassLoader())) {
        throw new SecurityException("Unsafe");
    }
}
```

The `Unsafe` category is a single case and provides a static method of `getUnsafe` to obtain the `Unsafe` instance. This appears to have been used to obtain the `Unsafe` instance. But when we call this static method, we throw out the `SecurityException` anomaly:

```bash
Exception in thread "main" java.lang.SecurityException: Unsafe
    at Unsafe.java:90
    at com.cn.test.GetUnsafeTest.main
```

**Why is the `public static` method not directly accessible?**

This is because, in the `getUnsafe` method, the caller's `classLoader` will be examined to determine whether the current class is loaded by `BootstrapClassLoader`, and if not, a `SecurityException` is thrown. That is, only those classes loaded by the bootstrap class loader can call methods in the Unsafe class to prevent them from being called in unreliable code.

**Why are there such cautious restrictions on the use of Unsafe?**

`Unsafe` provides too low a level of functionality (e.g., direct access to resources stored in the system, autonomous management of memory resources, etc.) and security risks are greater and, if used inappropriately, can easily be very serious.

**How should instances be obtained if the category `Unsafe` is used?**

Two possible options are presented here.

1. Use reflection to obtain an itemized individual object `theUnsafe` in Unsafe.

```java
try {
    Field field = Unsafe.class.getDeclaredField("theUnsafe");
    field.setAccessible(true);
    return (Unsafe) field.get(null);
} catch (Exception e) {
    Log.error(e.getMessage(), e);
}
```

2. Proceeding from the restrictions on the use of the `getUnsafe` method, by Java command line option `-Xbootclasspath/a` to add the category A of the Unsafe-related methods
