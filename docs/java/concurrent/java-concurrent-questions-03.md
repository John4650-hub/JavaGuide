I don't...
Title: Java with summary of common interview (low)
Category:
Tag:

- Java
  Head:
- Meta
- Name: keywords
  Content: Multi-line, deadlock, thread pool, CAS, AQS
- Meta
- Name: description
  It is hoped that this will help you!
  I don't...

@include: @article-header.snippet.md--

# ThreadLocal

# ThreadLocal

Usually, the variables we create can be accessed and modified by any thread. This may lead to data competition and linear safety in a multi-threaded environment. So, **how can this happen if you want to have your own local variable for each thread?**

The category `ThreadLocal` provided in JDK is intended to address this issue. **The `ThreadLocal` class allows each thread to bind its own value**, which can be graphically compared to a `data box`. Each thread has its own separate box to store private data to ensure that data between different threads do not interfere.

When you create a `ThreadLocal` variable, each thread that accesses the variable will have an independent copy. This is also the origin of the name `ThreadLocal`. Threads can obtain local copies of their own threads by means of `get()` or modify their value by means of `set()` so as to avoid linear security.

A simple example: Suppose two people go to the treasure house to collect the treasure. If they shared a bag, disputes would inevitably arise; but if everyone had an independent bag, there would be no problem. If these two people are compared to a thread, then `ThreadLocal` is the way to avoid the two threads competing for the same resource.

```Java
// This post is part of our special coverage Global Voices 2011.
Private static ThreadLocal<Integer> threadLocal = ThreadLocal.withInitial(() -> 0);

public static void main(String[] args) {
    Runnable task = () -> {
        int value = threadLocal.get();
        value += 1;
        threadLocal.set(value);
        System.out.println(Thread.currentThread().getName() + " Value: " + threadLocal.get());
    };

    Thread thread1 = new Thread(task, "Thread 1");
    Thread thread2 = new Thread(task, "Thread 2");

    thread1.start(); // Output:
    thread2.start(); // Output:
}
```

# Do you understand ThreadLocal's principles?

Start with the source code for the `Thread` class.

```Java
//...
// ThreadLocal values associated with this thread, maintained by ThreadLocal class
ThreadLocal.ThreadLocalMap threadLocals = null;

// Inheritable ThreadLocal values associated with this thread, maintained by InheritableThreadLocal class
ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
//...
```

From the source code of the `Thread` class above, it can be seen that one of the `threadLocals` and one of the `inheritableThreadLocals` variables, both of which are of the `ThreadLocalMap` type, can be understood as `ThreadLocalMap` as the customized `HashMap` of the `ThreadLocal` class. By default, both variables are null and are created only when the thread calls for the `set` or `get` methods of the `ThreadLocal` class, and in fact, we call upon the `get()` and `set()` methods of the `ThreadLocalMap` class.

`set()` methodology for the `ThreadLocal` class.

```Java
// Retrieving current request thread
Thread t = Thread.currentThread();
// Extract the threadLocals variable within the Thread class (Hash Table Structure)
ThreadLocalMap map = getMap(t);
if (map != null) {
    // Place values that will need to be stored in this HashMap
    map.set(this, value);
} else {
    createMap(t, value);
}
```

Through the above, we can conclude by speculation: **The final variable is placed in the `ThreadLocalMap` of the current thread and does not exist on `ThreadLocal`, which can be understood as a mere `ThreadLocalMap` envelope, transmitting the value of the variable.** The `ThreadLocal` class can be accessed through `Thread.currentThread()`, directly via `getMap(Thread t)`, the `ThreadLocalMap` object of the thread.

**Each `ThreadLocalMap` has a `ThreadLocalMap`, while `ThreadLocalMap` can be stored against `ThreadLocal` as key to key and the object as value.**

```Java
// ThreadLocalMap
//...
```

For example, when we declared two `ThreadLocal` objects in the same thread, `ThreadLocalMap` internals are using the only `ThreadLocalMap` storage data, `ThreadLocalMap` key is the `ThreadLocal` object, and value is the value set by the `ThreadLocal` object.

The `ThreadLocal` data structure is as follows:

\[ThreadLocal Data Structure
