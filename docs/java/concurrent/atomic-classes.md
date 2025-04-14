---
title: Summary of Atomic Classes
category: Java
tag:
  - Java Concurrency
---

## Introduction to Atomic Classes

`Atomic` translates to "atomic" in Chinese. In chemistry, an atom is the smallest unit of matter and is indivisible in chemical reactions. In programming, `Atomic` refers to an operation that has atomicity, meaning the operation is indivisible and uninterruptible. Even when multiple threads are executing simultaneously, the operation either completes entirely or does not execute at all, and other threads will not see a partially completed state.

In simple terms, atomic classes are classes that have the characteristic of atomic operations.

The `Atomic` classes in the `java.util.concurrent.atomic` package provide a thread-safe way to operate on a single variable.

The `Atomic` classes rely on CAS (Compare-And-Swap) optimistic locking to ensure the atomicity of their methods without needing traditional locking mechanisms (such as `synchronized` blocks or `ReentrantLock`).

This article will only introduce the concept of Atomic classes; for specific implementation principles, you can read the article written by the author: [Detailed Explanation of CAS](./cas.md).

![Overview of JUC Atomic Classes](https://oss.javaguide.cn/github/javaguide/java/JUC%E5%8E%9F%E5%AD%90%E7%B1%BB%E6%A6%82%E8%A7%88.png)

Based on the data types of the operations, the atomic classes in the JUC package can be divided into four categories:

**1. Basic Types**

Using atomic methods to update basic types

- `AtomicInteger`: Integer atomic class
- `AtomicLong`: Long atomic class
- `AtomicBoolean`: Boolean atomic class

**2. Array Types**

Using atomic methods to update an element in an array

- `AtomicIntegerArray`: Integer array atomic class
- `AtomicLongArray`: Long array atomic class
- `AtomicReferenceArray`: Reference type array atomic class

**3. Reference Types**

- `AtomicReference`: Reference type atomic class
- `AtomicMarkableReference`: Atomic update of a reference type with a mark. This class associates a boolean mark with a reference, ~~and can also solve the ABA problem that may occur when using CAS for atomic updates~~.
- `AtomicStampedReference`: Atomic update of a reference type with a version number. This class associates an integer value with a reference, which can be used to solve the atomic update of data and the version number of the data, and can address the ABA problem that may occur when using CAS for atomic updates.

**🐛 Correction (see: [issue#626](https://github.com/Snailclimb/JavaGuide/issues/626))**: `AtomicMarkableReference` cannot solve the ABA problem.

**4. Object Property Modification Types**

- `AtomicIntegerFieldUpdater`: Atomic updater for integer fields
- `AtomicLongFieldUpdater`: Atomic updater for long fields
- `AtomicReferenceFieldUpdater`: Atomic updater for fields in reference types

## Basic Type Atomic Classes

Using atomic methods to update basic types

- `AtomicInteger`: Integer atomic class
- `AtomicLong`: Long atomic class
- `AtomicBoolean`: Boolean atomic class

The methods provided by the above three classes are almost identical, so we will use `AtomicInteger` as an example for introduction.

**Common Methods of `AtomicInteger`**:

```java
public final int get() // Get the current value
public final int getAndSet(int newValue) // Get the current value and set a new value
public final int getAndIncrement() // Get the current value and increment it
public final int getAndDecrement() // Get the current value and decrement it
public final int getAndAdd(int delta) // Get the current value and add the expected value
boolean compareAndSet(int expect, int update) // If the input value equals the expected value, atomically set that value to the input value (update)
public final void lazySet(int newValue) // Eventually set to newValue; lazySet provides a weaker semantic than set, which may allow other threads to read the old value for a short period afterward, but may be more efficient.
```

**Example of Using `AtomicInteger`**:

```java
// Initialize AtomicInteger object with an initial value of 0
AtomicInteger atomicInt = new AtomicInteger(0);

// Use getAndSet method to get the current value and set the new value to 3
int tempValue = atomicInt.getAndSet(3);
System.out.println("tempValue: " + tempValue + "; atomicInt: " + atomicInt);

// Use getAndIncrement method to get the current value and increment by 1
tempValue = atomicInt.getAndIncrement();
System.out.println("tempValue: " + tempValue + "; atomicInt: " + atomicInt);

// Use getAndAdd method to get the current value and add the specified value 5
temp