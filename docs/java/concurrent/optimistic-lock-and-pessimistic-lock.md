I don't...
Title: Optimistic and Pessimistic Locks
Category:
Tag:

- Java
  I don't...

If the Pessimistic Lock and Optimistic Lock were to be matched to real life, pessimism is a bit like a pessimistic (or pre-emptive) person who always assumes the worst and avoids problems. The Optimistic Lock is a bit like a more optimistic person, who would always assume the best and quickly resolve the problem before it arose.

What's a Pessimistic Lock?

The Pessimistic Lock always assumes that the worst-case scenario is that every time a shared resource is visited, problems arise (e.g., when the shared data is modified). So, every time a resource is accessed, the lock is engaged, blocking other threads that want to access the resource until the lock is released by the previous holder. That is to say, **the shared resources are given only one line at a time; other threads are blocked and then transferred to the other.**

Monopoly locks such as `synchronized` and `ReentrantLock` in Java are the realization of pessimism.

```java
public void performSynchronizedTask() {
    synchronized (this) {
        // Need sync operation
        System.out.println("I'm sorry");
    }
}

private Lock lock = new ReentrantLock();
lock.lock();
try {
    // Need sync operation
} finally {
    lock.unlock();
    System.out.println("I'm sorry");
}
```

In high-contention scenarios, strong competition for locks can lead to deadlocks, and a large number of blockages can lead to context switching in systems, increasing their performance costs. Moreover, Pessimistic Locks may have a deadlock problem (the threads are locked in different sequences), affecting the proper operation of the code.

# What's an Optimistic Lock?

The Optimistic Lock always assumes that the best-case scenario is that the shared resources are free of problems every time they are visited, allowing the thread to operate without locking or waiting. However, the corresponding resources (i.e., data) may be modified by other threads at the time of submission of the modification (the specific method can be the version number mechanism or CAS algorithm).

The atomic variants under the `java.util.concurrent.atomic` package in Java (e.g., `AtomicInteger`, `LongAdder`) are a way of achieving it using the Optimistic Lock **CAS**.
[JUC atoms overview](https://oss.javaguide.cn/github/javaguide/java/JUC%E5%9F%BA%E7%A1%80%E7%B1%BB%E5%9B%BE-202304052168.png)

```java
// LongAdder will perform better than AtomicInteger and AtomicLong in a high-contention scenario
// The cost is to consume more memory space (space for time)
LongAdder sum = new LongAdder();
sum.increment();
```

In a high-contention scenario, optimism is more likely to prevail than pessimism to the extent that the absence of a lock on competition leads to a line blockage or a deadlock. However, if there is a high incidence of conflict (in a very high percentage of cases), there will be frequent failures and retries, which will also significantly affect performance, leading to a surge in CPU usage.

However, a large number of failed attempts can also be resolved, as we mentioned earlier, by `LongAdder` in space for time.

In theory:

- The Pessimistic Lock is often used in write-heavy cases (multi-write scenarios, intense competition), which avoids frequent failures and retries that influence performance, and the Pessimistic Lock is fixed. However, if Optimistic Locks solve the problem of frequent failures and retries (e.g., `LongAdder`), they may also be considered for use, depending on the actual situation.
- Optimistic Locks are often used in read-heavy cases (more reads and less competition), thus avoiding frequent lockings that affect performance. However, the Optimistic Lock is primarily aimed at individual shared variables (reference to the atomic variable class under the `java.util.concurrent.atomic` package).

# How to Implement Optimistic Locks?

Optimistic Locks are usually achieved using version number mechanisms or CAS algorithms, and CAS algorithms are relatively more common and require special attention here.

Version Number Mechanism

Usually, a data version number `version` field is added to the data table, indicating the number of times the data has been modified. When data is modified, the `version` value is incremented. When Line A is to update the data values, the `version` values are also read while reading the data. When submitting the update, the `version` values that have just been read are updated only when the `version` values in the current database are equal; otherwise, the update is retried until it is successful.

**A simple example**: Assuming there is a version field in the account information table in the database, the current value is 1; the current account balance field (`balance`) is $100
