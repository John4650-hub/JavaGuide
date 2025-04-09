I don't...
Title: Java 8 New Features
Category:
Tag:

- Java's new character.
  I don't...

> This contribution from [cowbi](https://github.com/cowbi) ~

- markdownlint-disable MD024--

Oracle published Java 8 (jdk1.8) in 2014, for many reasons making it the most used jdk version in the current market. It's been almost seven years since it was released, but many programmers are not sufficiently aware of its new features, especially old programmers like me, who used Java 8 before.

In order not to leave the ranks too far, it would be necessary to summarize these new features. It changes or optimizes much more than jdk 7 — for example, it can have static methods and methods in an interface, which subverts previous perceptions; `java.util.HashMap` data structures with red-black trees; and well-known Lambda expressions. This paper does not share all the new features, but only lists the new ones that are more commonly used for your detailed presentation. For more information, please see [an introduction to the new features of Java 8 on the Web](https://www.oracle.com/java/technology/javasse/8-whats-new.html).

# Interface

Interface was originally designed to be abstract and to enhance expansion. And that leaves a little regret that when the interface changes, the class that implements it has to change.

In order to address the existing incompatibility with the modifications to the interface, the new interface approach can be modified by `default` or `static` so that there is a method and the class of implementation does not have to be rewritten.

There are several ways in which an interface can be modified by them, and the difference between these two modifiers is mainly the difference between normal and static methods.

`default` modifications are common examples and can be called `this` and be inherited and rewritten by subclasses. `static` modifications are applied in the same way as general static methods. However, it cannot be inherited by subclasses and can only be called by `Interface`.

Let us look at a practical example.

```Java
// This post is part of our special coverage Libya 2011.
void sm() {
    System.out.println("interface offers");
}
// I'm sorry
void sm2() {
    System.out.println("interface offers");
}
// I'm sorry

default void def() {
    System.out.println("interface method");
    // I'm sorry
}
default void def2() {
    System.out.println("interface method 2");
    // I'm sorry
    // Subject to reclassification
    void f();
    // I'm sorry

// This post is part of our special coverage New1.
default void def() {
    System.out.println("Interface New1 method");
    // I'm sorry
    // I'm sorry
}
```

If one class implements both the `Interface New` interface and the `Interface New1` interface, they both have `def()` and the `Interface New` interface and the `Interface New1` interface have no inheritance relationship, then `def()` must be rewritten. Otherwise, the compilation will be incorrect.

```Java
public class NewImpl implements InterfaceNew, InterfaceNew1 {
    public static void main(String[] args) {
        InterfaceNew interfaceNew = new NewImpl();
        interfaceNew.def();
        // I'm sorry
    }

    @Override
    public void def() {
        InterfaceNew1.super.def();
        // I'm sorry
    }

    @Override
    // Other Organiser
    // I'm sorry
    // I'm sorry
}
```

**In Java 8, what's the difference between interfaces and abstract classes?**

A lot of small partners think, "Well, since an interface can do it in its own way, it doesn't seem to be much different from an abstract class."

Actually, they're different.

1. The difference between an interface and a class, which appears to be trivial, is mainly:

- Multiple interfaces, single inheritance
- The method for the interface is to modify the public interface, and the variable is to modify the public static final. An abstract class can have other modifiers.

2. An interface's approach is more like an extension plugin. An abstract class is going to be inherited.

At the outset, we also mentioned that the addition of `default` and `static` modifiers to the interface was not intended to replace `abstract class`, in order to address the existing problems of incompatibility with the modification of the interface. For use, use an abstract class or use an interface, and do not replace it with the new interface feature.

**Remember that the interface is never the same as the class.**

# Functional Interface

**Definition**: Also known as SAM interface, or Single Abstract Method interfaces, there is and there is only one abstract method, but there can be multiple non-abstract methods.

There is a special package for functional interfaces `java.util.function` in Java 8, and all interfaces under this package have `@FunctionalInterface` annotation to provide
