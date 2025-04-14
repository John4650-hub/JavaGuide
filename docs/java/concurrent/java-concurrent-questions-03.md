---
title: Summary of Common Java Concurrency Interview Questions (Part 2)
category: Java
tag:
  - Java Concurrency
head:
  -   - meta
      - name: keywords
        content: multithreading, deadlock, thread pool, CAS, AQS
  -   - meta
      - name: description
        content: A summary of common knowledge points and interview questions in Java concurrency (with detailed answers), hope it helps you!
---

<!-- @include: @article-header.snippet.md -->

## ThreadLocal

### What is the use of ThreadLocal?

In general, the variables we create can be accessed and modified by any thread. This can lead to data races and thread safety issues in a multithreaded environment. So, **how can we ensure that each thread has its own dedicated local variable?**

The `ThreadLocal` class provided in the JDK is designed to solve this problem. **The `ThreadLocal` class allows each thread to bind its own value**, which can be metaphorically described as a "box for storing data." Each thread has its own independent box for storing private data, ensuring that data does not interfere between different threads.

When you create a `ThreadLocal` variable, each thread accessing that variable will have its own independent copy. This is also the origin of the name `ThreadLocal`. Threads can retrieve their local copy using the `get()` method or modify the value of that copy using the `set()` method, thus avoiding thread safety issues.

For example, suppose there are two people collecting treasures from a treasure house. If they share a bag, disputes are inevitable; but if each person has their own independent bag, this problem will not occur. If we compare these two people to threads, then `ThreadLocal` is the method used to avoid competition for the same resource between these two threads.

```java
public class ThreadLocalExample {
    private static ThreadLocal<Integer> threadLocal = ThreadLocal.withInitial(() -> 0);

    public static void main(String[] args) {
        Runnable task = () -> {
            int value = threadLocal.get();
            value += 1;
            threadLocal.set(value);
            System.out.println(Thread.currentThread().getName() + " Value: " + threadLocal.get());
        };

        Thread thread1 = new Thread(task, "Thread-1");
        Thread thread2 = new Thread(task, "Thread-2");

        thread1.start(); // Output: Thread-1 Value: 1
        thread2.start(); // Output: Thread-2 Value: 1
    }
}
```

### ⭐️ Do you understand the principles of ThreadLocal?

Starting from the source code of the `Thread` class.

```java
public class Thread implements Runnable {
    //......
    // ThreadLocal values related to this thread. Maintained by the ThreadLocal class
    ThreadLocal.ThreadLocalMap threadLocals = null;

    // InheritableThreadLocal values related to this thread. Maintained by the InheritableThreadLocal class
    ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
    //......
}
```

From the above source code of the `Thread` class, we can see that there is a `threadLocals` and an `inheritableThreadLocals` variable, both of which are of type `ThreadLocalMap`. We can understand `ThreadLocalMap` as a customized `HashMap` implemented by the `ThreadLocal` class. By default, both variables are null, and they are only created when the current thread calls the `set` or `get` methods of the `ThreadLocal` class. In fact, when we call these two methods, we are calling the corresponding `get()` and `set()` methods of the `ThreadLocalMap` class.

The `set()` method of the `ThreadLocal` class

```java
public void set(T value) {
    // Get the current requesting thread
    Thread t = Thread.currentThread();
    // Retrieve the threadLocals variable (hash table structure) inside the Thread class
    ThreadLocalMap map = getMap(t);
    if (map != null)
        // Store the value to be saved in this hash table
        map.set(this, value);
    else
        createMap(t, value);
}
ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}
```

From the above content, we can conclude: **The final variable is stored in the current thread's `ThreadLocalMap`, not on the `ThreadLocal`. `ThreadLocal` can be understood as just a wrapper for `ThreadLocalMap`, passing the variable value.** The `ThreadLocal` class can access the current thread's `ThreadLocalMap` object directly through `Thread.currentThread()` and then call `getMap(Thread t)`.

\*\*Each `Thread` has a `ThreadLocalMap`, and `ThreadLocalMap` can store key-value pairs with `ThreadLocal` as the
