I don't...
type: CAS details
Category:
Tag:
- Java.
I don't...

The introduction of the optimistic and pessimistic locks, as well as the usual ways in which the optimistic lock can be achieved, can be read in the article by the writer: [optimistic and pessimistic locks](https://javaguide.cn/java/concurrent/optimistic-lock-and-pessimistic-lock.html).

This article focuses on the implementation of CAS in Java and some of the problems that exist in CAS.

# How did Java achieve the CAS?

In Java, a key class for achieving CAS (Compare-And-Swap, compare and exchange) operations is `Unsafe`.

The class `Unsafe` is located under the `sun.misc` package and is a class that provides low-level, unsafe operations. Because of its powerful functions and potential hazards, it is usually used within the JVM or in libraries that require very high performance and low-level access, rather than being recommended for use by ordinary developers in applications. For a detailed description of the `Unsafe` class, the article can be read: [Unsafe](https://javaguide.cn/java/basis/unsafe.html).

The `Unsafe` class under the `sun.misc` package provides the CAS methods `compareAndSwapObject`, `compareAndSwapInt`, and `compareAndSwapLong` for the `Object`, `int`, and `long` type operations:

```Java
/**
 * Updates the value of object fields in atomic form.
 * @param o object to operate on
 * @param offset memory offset for the object field
 * @param expected old desired value
 * @param x new value to set
 * @return returns true if value is successfully updated; otherwise returns false
 */
Boolean compareAndSwapObject(Object o, long offset, Object expected, Object x);

/**
 * Updates the value of the object field of the int type in an atomic manner.
 */
Boolean compareAndSwapInt(Object o, long offset, int expected, int x);

/**
 * Updates the values of the long-type object fields in atomic form.
 */
Boolean compareAndSwapLong(Object o, long offset, long expected, long x);
```

The CAS methods in the `Unsafe` class are `native` methods. The `native` keyword indicates that these methods are implemented in native code (usually C or C++) rather than Java. These methods directly call the low-level hardware instructions for atomic operations. In other words, Java is not directly used to achieve CAS.

More precisely, the CAS in Java is in the form of C++ inline compilation, which is called through JNI. Thus, the concrete realization of CAS is closely related to the operating system and the CPU.

The `java.util.concurrent.atomic` package provides a number of classes for atomic operations.

![JUC atoms overview](https://oss.javaguide.cn/github/javaguide/java/JUC%E5%85%A8%E8%A6%81%E7%9B%AE%E5%BD%95%E5%9B%BE%E7%89%87.png)

For the introduction and use of these atomic classes, the article can be read: [Atomic Classes Summary](https://javaguide.cn/java/concurrent/atomic-classes.html).

The atomic classes rely on the CAS optimistic lock to ensure the atomicity of their methods without the need to use traditional locking mechanisms (e.g., `synchronized` blocks or `ReentrantLock`).

`AtomicInteger` is one of Java's atomic classes, which is used mainly for the atomic operation of `int`-type variables, and it uses low-level atomic operating methods provided by the `Unsafe` class to achieve the safety of the unlocked linear course.

Here, we interpret the core source code of `AtomicInteger` (JDK 1.8) to explain how Java uses the `Unsafe` approach to achieve atomic operations.

`AtomicInteger` core source code as follows:

```Java
// Get Unsafe instance
Unsafe unsafe = Unsafe.getUnsafe();

// Get a memory offset for the "value" field in the AtomicInteger class
long valueOffset = unsafe.objectFieldOffset(AtomicInteger.class.getDeclaredField("value"));

// Ensure visibility of the "value" field
// If the current value equals the expected value, set the atomic value to newValue
// Use Unsafe#compareAndSwapInt for CAS operations
int currentValue, newValue;
currentValue = unsafe.compareAndSwapInt(this, valueOffset, expectedValue, newValue);

// Atomically add the current value to delta and return the old value
return unsafe.getAndAddInt(this, valueOffset, delta);

// Atomically add the current value to 1 and return the previous value (old)
// Use Unsafe#getAndAddInt for CAS operations.
public final int getAndIncrement() {
    return unsafe.getAndAddInt(this, valueOffset, 1);
}

// Atomically subtract 1 from the current value and return the value before the reduction (old value)
public final int getAndDecrement() {
    return