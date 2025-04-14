---
title: Practical Application of Java 8 New Features
category: Java
tag:
  - Java New Features
---

> This article is contributed by [cowbi](https://github.com/cowbi)~

<!-- markdownlint-disable MD024 -->

Oracle released Java 8 (jdk1.8) in 2014, and for various reasons, it has become the most widely used jdk version in the market. Although it has been nearly 7 years since its release, many programmers are still not well acquainted with its new features, especially those veteran programmers who are used to versions prior to Java 8, like myself.

To avoid falling too far behind, it is necessary to summarize and organize these new features. There are many changes or optimizations compared to jdk.7, such as the ability to have static methods with method bodies in interfaces, which overturns previous understandings; the addition of red-black trees in the `java.util.HashMap` data structure; and the well-known Lambda expressions, among others. This article cannot cover all new features in detail but will list and explain the more commonly used ones. For more related content, please refer to the [official introduction to Java 8 new features](https://www.oracle.com/java/technologies/javase/8-whats-new.html).

## Interface

The original design intention of interfaces is to focus on abstraction and enhance extensibility. However, there is a slight regret that when an interface is modified, the classes implementing it must also be modified.

To solve the compatibility issue between interface modifications and existing implementations, new methods in interfaces can be modified with `default` or `static`, allowing them to have method bodies, and implementing classes do not need to override these methods.

An interface can have multiple methods modified by these two modifiers, and the main difference between them is that one is an instance method and the other is a static method.

1. Methods modified by `default` are ordinary instance methods that can be called using `this` and can be inherited and overridden by subclasses.
1. Methods modified by `static` are used in the same way as static methods in regular classes. However, they cannot be inherited by subclasses and can only be called using `Interface`.

Let's look at a practical example.

```java
public interface InterfaceNew {
    static void sm() {
        System.out.println("Implemented by interface");
    }
    static void sm2() {
        System.out.println("Implemented by interface");
    }

    default void def() {
        System.out.println("Interface default method");
    }
    default void def2() {
        System.out.println("Interface default2 method");
    }
    // Must be overridden by implementing class
    void f();
}

public interface InterfaceNew1 {
    default void def() {
        System.out.println("InterfaceNew1 default method");
    }
}
```

If a class implements both `InterfaceNew` and `InterfaceNew1`, and both have `def()`, and there is no inheritance relationship between `InterfaceNew` and `InterfaceNew1`, then `def()` must be overridden. Otherwise, a compilation error will occur.

```java
public class InterfaceNewImpl implements InterfaceNew, InterfaceNew1 {
    public static void main(String[] args) {
        InterfaceNewImpl interfaceNew = new InterfaceNewImpl();
        interfaceNew.def();
    }

    @Override
    public void def() {
        InterfaceNew1.super.def();
    }

    @Override
    public void f() {
    }
}
```

**What is the difference between interfaces and abstract classes in Java 8?**

Many friends think: "Since interfaces can also have their own method implementations, they seem to be not much different from abstract classes."

In fact, there are still differences:

1. The difference between interfaces and classes is somewhat trivial, mainly:

   - Interfaces can be implemented multiple times, while classes can only inherit once.
   - Methods in interfaces are public abstract, and variables are public static final. Abstract classes can use other modifiers.

1. Methods in interfaces are more like extension plugins, while methods in abstract classes are meant to be inherited.

As mentioned earlier, the new `default` and `static` methods in interfaces were introduced to solve the compatibility issue between interface modifications and existing implementations, not to replace `abstract classes`. In usage, where an abstract class should be used, it should still be used; do not replace it just because of the new features of interfaces.

**Remember that interfaces are always different from classes.**

## Functional Interface

**Definition**: Also known as SAM interfaces, i.e., Single Abstract Method interfaces, which have exactly one abstract method but can have multiple non-abstract methods.

In Java 8, there is a dedicated package for functional interfaces, `java.util.function`, where all interfaces are annotated with `@FunctionalInterface`, providing functional programming capabilities.

There are also functional interfaces in other packages, some of which do not have the `@FunctionalInterface` annotation, but as long as they meet the definition of a functional interface,
