I don't...
title: Class Loader Detail (focus)
Category:
Tag:

- JVM.
  I don't...

# Look back at the loading process

A brief review of the loading process before the introduction of the type loader and the parental assignment model is initiated.

- Class loading process: **Load -> Connection -> Initialization**.
- The connection process can be divided into three steps: **Validation -> Preparation -> Resolution.**

(https://oss.javaguide.cn/github/javaguide/java/jvm/class-loading-processure.png)

Loading is the first step in the type loading process, with the following three main things being accomplished:

1. Acquiring the definition of such binary bytes by a full name
1. Operation-time data structure for converting static storage structures represented by bytes to the method area
1. Generate a `Class` object in the memory that represents this category as the access point for these data in the methodology area

# Class loader

## Type loader description

The type loader appears from JDK 1.0, initially to meet the needs of Java Applet. Later, it became an important part of the Java process, giving Java class the ability to be dynamically loaded and implemented in JVM.

According to the official API document:

> A class loader is an object that is responsible for loading classes. The class loader is an abstract class.
>
> Every Class object refers to a reference to the ClassLoader that created it.
>
> Class objects for all purposes are not created by class loaders, but they are created by the Java runtime. The class loader for an array class, as it was returned by Class.getClassLoader, is the same as the class loader for its component type; if the component type is a primitive type, then the array class has no class type.

The translation probably means:

> Class Loader is an object responsible for loading classes. `Class Loader` is an abstract class. The binary name of the given class, the class loader shall attempt to locate or generate data that constitute the class definition. The typical policy is to convert the name to a filename and then read the "class file" of the name from the file system.
>
> Each Java class has a reference pointing to its loading `ClassLoader`. However, the array class was not created through `ClassLoader`, but JVM automatically created when required, and the array class was consistent with `ClassLoader` when it was acquired through `getClassLoader()` method.

As can be seen from the above presentation:

- The Class Loader is the object responsible for loading the class for this step in achieving the Class Loading.
- Each Java class has a reference pointing to its loading `ClassLoader`.
- Array classes are not created by `ClassLoader` (no corresponding binary bytes for the array) and are generated directly by JVM.

```Java
Class
I don't know.
{\cHFFFFFF}{\cH00FFFF} {\cHFFFFFF}{\cH00FFFF} {\cHFFFFFF}{\cH00FFFF}{\cH00FFFF}
@CallerSensitive
public ClassLoader getClassLoader()
/...
♪ I'm sorry ♪
I don't know.
♪ I'm sorry ♪
```

In short, the main function of the **class loader is to dynamically load the bytecode of the Java class (`.class` files) to JVM (to generate a `Class` object in the memory that represents that class).** The bytecode may be compiled by the Java Source (`.java` file) by `javac`, or generated through the dynamic production of tools or downloaded through the Internet.

In fact, in addition to the loader class, the type loader can load the resource requirements for Java applications, such as text, images, configuration files, videos, etc. This paper only discusses its core function: loading classes.

## Class loader rules

JVM does not load all classes once, but dynamically as needed. In other words, most classes are loaded only when they are specifically used, which makes the memory more efficient.

For already loaded classes, they will be placed in `ClassLoader`. When the class is loaded, the system first determines whether the current class has been loaded. The already loaded class returns directly; otherwise, it will be attempted. That is, for a class loader, a class of the same binary name will be loaded only once.

```Java
I can't believe it.
I don't know.
I don't know.
// Class loads by this type of loader.
{\bord0\shad0\alphaH3D} private final Victor
// Called by VM to record each loaded class with such loader.
void addClass
Classes.addElement(c);
♪ I'm sorry ♪
I don't know.
♪ I'm sorry ♪
```

## Class loader Summary

`ClassLoader`:

1. **`BootstrapClassLoader` (starter class loader)**: maximum load class, performed by C++, usually expressed as null and without parental grade, primarily used to load core libraries within JDK (`rt.jar`, `resources.jar`, `charsets.jar`, and other
