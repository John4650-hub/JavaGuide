---
title: Detailed Explanation of CAS
category: Java
tag:
  - Java Concurrent
---

An introduction to optimistic and pessimistic locks, as well as common implementation methods of optimistic locks, can be found in this article written by the author: [Detailed Explanation of Optimistic and Pessimistic Locks](https://javaguide.cn/java/concurrent/optimistic-lock-and-pessimistic-lock.html).

This article mainly introduces the implementation of CAS (Compare-And-Swap) in Java and some issues associated with CAS.

## How is CAS Implemented in Java?

In Java, a key class for implementing CAS is `Unsafe`.

The `Unsafe` class is located in the `sun.misc` package and provides low-level, unsafe operations. Due to its powerful functionalities and potential dangers, it is typically used internally in the JVM or in libraries that require extremely high performance and low-level access, and is not recommended for use by average developers in applications. For a detailed introduction to the `Unsafe` class, you can read this article: 📌[Detailed Explanation of Java Magic Class Unsafe](https://javaguide.cn/java/basis/unsafe.html).

The `Unsafe` class under the `sun.misc` package provides methods like `compareAndSwapObject`, `compareAndSwapInt`, and `compareAndSwapLong` for implementing CAS operations on `Object`, `int`, and `long` types:

```java
/**
 * Atomically updates the value of an object field.
 *
 * @param o        The object to operate on
 * @param offset   The memory offset of the object field
 * @param expected The expected old value
 * @param x        The new value to set
 * @return true if the value was successfully updated; otherwise, false
 */
boolean compareAndSwapObject(Object o, long offset, Object expected, Object x);

/**
 * Atomically updates the value of an int type object field.
 */
boolean compareAndSwapInt(Object o, long offset, int expected, int x);

/**
 * Atomically updates the value of a long type object field.
 */
boolean compareAndSwapLong(Object o, long offset, long expected, long x);
```

The CAS methods in the `Unsafe` class are `native` methods. The `native` keyword indicates that these methods are implemented in native code (usually C or C++) rather than in Java. These methods directly call low-level hardware instructions to achieve atomic operations. In other words, the CAS operations are not directly implemented in Java.

More precisely, CAS in Java is implemented in the form of C++ inline assembly and is called via JNI (Java Native Interface). Therefore, the specific implementation of CAS is closely related to the operating system and the CPU.

The `java.util.concurrent.atomic` package provides several classes for atomic operations.

![Overview of JUC Atomic Classes](https://oss.javaguide.cn/github/javaguide/java/JUC%E5%8E%9F%E5%AD%90%E7%B1%BB%E6%A6%82%E8%A7%88.png)

For an introduction to these Atomic classes and their usage, you can read this article: [Summary of Atomic Classes](https://javaguide.cn/java/concurrent/atomic-classes.html).

Atomic classes rely on CAS optimistic locks to ensure the atomicity of their methods without the need for traditional locking mechanisms such as `synchronized` blocks or `ReentrantLock`.

`AtomicInteger` is one of Java's atomic classes, mainly used for atomic operations on `int` type variables. It leverages low-level atomic operation methods provided by the `Unsafe` class to achieve lock-free thread safety.

Next, we will explain how Java uses methods from the `Unsafe` class to implement atomic operations by interpreting the core source code of `AtomicInteger` (JDK1.8).

The core source code of `AtomicInteger` is as follows:

```java
// Get Unsafe instance
private static final Unsafe unsafe = Unsafe.getUnsafe();
private static final long valueOffset;

static {
    try {
        // Get the memory offset of the "value" field in the AtomicInteger class
        valueOffset = unsafe.objectFieldOffset
            (AtomicInteger.class.getDeclaredField("value"));
    } catch (Exception ex) { throw new Error(ex); }
}
// Ensure the visibility of the "value" field
private volatile int value;

// Atomically sets the value to newValue if the current value equals expected
// Uses Unsafe#compareAndSwapInt method for CAS operation
public final boolean compareAndSet(int expect, int update) {
    return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
}

// Atomically adds delta to the current value and returns the old value
public final int getAndAdd(int delta) {
    return unsafe.getAndAddInt(this, valueOffset, delta);
}

// Atomically adds 1 to the current value and returns the value before addition (old value)
// Uses Unsafe#getAndAddInt method for CAS operation.
public final int getAndIncrement() {
    return unsafe.getAndAddInt(this, valueOffset, 1);
}

// Atomically subtracts 1 from the current value and returns the value before subtraction (old value)
public final int getAndDecrement() {
    return unsafe.getAndAddInt(this, valueOffset, -1);
}
```

Source code of `Unsafe#getAndAddInt`:

```java
// Atomically gets and adds an integer value
public final int getAndAddInt(Object o, long offset, int delta) {
    int v;
    do {
        // Get the integer value at memory offset of o in a volatile manner
        v = getIntVolatile(o, offset);
    } while (!compareAndSwapInt(o, offset, v, v + delta));
    // Return the old value
    return v;
}
```

It can be seen that `getAndAddInt` uses a `do-while` loop: it continually retries until the `compareAndSwapInt` operation succeeds. In other words, the `getAndAddInt` method attempts to update the value using the `compareAndSwapInt` method. If the update fails (because the value has been modified by another thread in the meantime), it retrieves the current value again and retries the update until the operation succeeds.

Since CAS operations may fail due to concurrent conflicts, they are often used in conjunction with a `while` loop to keep retrying until the operation succeeds. This is known as **spin-lock mechanism**.

## What Issues Exist with CAS Algorithms?

The ABA problem is the most common issue with CAS algorithms.

### ABA Problem

If a variable V is initially read as value A, and when preparing to assign a new value, it checks that it is still A, can we conclude that its value has not been modified by other threads? Clearly, this is not the case, because during that period, its value may have been changed to another value and then back to A, causing the CAS operation to mistakenly assume it was never modified. This issue is referred to as the **"ABA" problem** of CAS operations.

The solution to the ABA problem is to prepend a **version number or timestamp** to the variable. The `AtomicStampedReference` class introduced in JDK 1.5 is designed to solve the ABA problem, where the `compareAndSet()` method first checks if the current reference equals the expected reference and if the current stamp equals the expected stamp. If both are equal, it atomically sets the reference and stamp to the provided update values.

```java
public boolean compareAndSet(V expectedReference,
                             V newReference,
                             int expectedStamp,
                             int newStamp) {
    Pair<V> current = pair;
    return
        expectedReference == current.reference &&
        expectedStamp == current.stamp &&
        ((newReference == current.reference &&
          newStamp == current.stamp) ||
         casPair(current, Pair.of(newReference, newStamp)));
}
```

### Long Spin Time and High Overhead

CAS often uses spinning to retry, meaning it keeps looping until it succeeds. If it fails for a long time, it can cause significant execution overhead for the CPU.

If the JVM can support the `pause` instruction provided by the processor, the efficiency of spin operations will be improved. The `pause` instruction has two key benefits:

1. **Delaying Pipeline Execution Instructions**: The `pause` instruction can delay the execution of instructions, thereby reducing CPU resource consumption. The specific delay time depends on the implementation version of the processor; on some processors, the delay may be zero.
1. **Avoiding Memory Ordering Conflicts**: When exiting the loop, the `pause` instruction can prevent the CPU pipeline from being flushed due to memory ordering conflicts, thus improving CPU execution efficiency.

### Can Only Ensure Atomic Operations for One Shared Variable

CAS operations can only effectively operate on a single shared variable. When multiple shared variables need to be manipulated, CAS becomes ineffective. However, starting from JDK 1.5, Java provides the `AtomicReference` class, which allows us to ensure the atomicity of references between objects. By encapsulating multiple variables within a single object, we can use `AtomicReference` to perform CAS operations.

In addition to this method via `AtomicReference`, locking can also be used to ensure atomicity.

## Summary

In Java, CAS is implemented through `native` methods in the `Unsafe` class, which directly call low-level hardware instructions to complete atomic operations. Because its implementation relies on C++ inline assembly and JNI calls, the specific implementation of CAS is closely related to the operating system and CPU.

While CAS has high-efficiency lock-free characteristics, it is also essential to be aware of issues such as the ABA problem and the high overhead caused by long spin times.
