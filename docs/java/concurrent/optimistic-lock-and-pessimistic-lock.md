---
title: Detailed Explanation of Optimistic Lock and Pessimistic Lock
category: Java
tag:
  - Java Concurrency
---

If we relate pessimistic locks and optimistic locks to real life, a pessimistic lock is like a rather pessimistic (or cautious) person who always assumes the worst-case scenario to avoid problems. An optimistic lock is like a more optimistic person who always assumes the best-case scenario and quickly resolves issues before they arise.

## What is a Pessimistic Lock?

A pessimistic lock always assumes the worst-case scenario, believing that problems (such as shared data being modified) will occur every time shared resources are accessed. Therefore, it locks the resource every time it is accessed, causing other threads that want to access this resource to block until the lock is released by the current holder. In other words, **shared resources are only available to one thread at a time, while other threads are blocked until the resource is released**.

In Java, exclusive locks like `synchronized` and `ReentrantLock` are implementations of the pessimistic lock concept.

```java
public void performSynchronisedTask() {
    synchronized (this) {
        // Synchronized operations
    }
}

private Lock lock = new ReentrantLock();
lock.lock();
try {
   // Synchronized operations
} finally {
    lock.unlock();
}
```

In high-concurrency scenarios, intense lock contention can cause thread blocking, and a large number of blocked threads can lead to context switching, increasing the performance overhead of the system. Additionally, pessimistic locks may also encounter deadlock issues (when threads acquire locks in an improper order), affecting the normal operation of the code.

## What is an Optimistic Lock?

An optimistic lock always assumes the best-case scenario, believing that problems will not occur every time shared resources are accessed. Threads can execute continuously without locking or waiting, only verifying whether the corresponding resource (i.e., data) has been modified by other threads when submitting changes (specific methods can use version number mechanisms or CAS algorithms).

In Java, atomic variable classes under the `java.util.concurrent.atomic` package (such as `AtomicInteger` and `LongAdder`) are implementations of optimistic locks using the **CAS** mechanism.
![Overview of JUC Atomic Classes](https://oss.javaguide.cn/github/javaguide/java/JUC%E5%8E%9F%E5%AD%90%E7%B1%BB%E6%A6%82%E8%A7%88-20230814005211968.png)

```java
// LongAdder performs better than AtomicInteger and AtomicLong in high-concurrency scenarios
// The trade-off is that it consumes more memory space (space for time)
LongAdder sum = new LongAdder();
sum.increment();
```

In high-concurrency scenarios, optimistic locks do not have the thread blocking caused by lock contention, nor do they have deadlock issues, often resulting in better performance. However, if conflicts occur frequently (in cases with a high write ratio), it may lead to frequent failures and retries, which can also significantly impact performance and cause CPU spikes.

However, the issue of frequent failures and retries can also be addressed. For example, as mentioned earlier, `LongAdder` solves this problem by trading space for time.

Theoretically:

- Pessimistic locks are usually used in scenarios with a high number of writes (intense write scenarios) to avoid the performance impact of frequent failures and retries, and the overhead of pessimistic locks is fixed. However, if optimistic locks can solve the frequent failure and retry issue (like `LongAdder`), they can also be considered based on the actual situation.
- Optimistic locks are typically used in scenarios with fewer writes (more read scenarios, less contention) to avoid the performance impact of frequent locking. However, optimistic locks mainly target single shared variables (refer to atomic variable classes under the `java.util.concurrent.atomic` package).

## How to Implement an Optimistic Lock?

Optimistic locks are generally implemented using version number mechanisms or CAS algorithms, with CAS algorithms being more common, which requires special attention.

### Version Number Mechanism

Typically, a `version` field is added to the data table to indicate the number of times the data has been modified. When the data is modified, the `version` value is incremented. When thread A wants to update the data value, it reads the `version` value at the same time. When submitting the update, it only updates if the `version` value read earlier matches the current `version` value in the database; otherwise, it retries the update operation until successful.

**A simple example**: Suppose there is a `version` field in the account information table in the database, with the current value being 1, and the current account balance field (`balance`) being $100.

1. Operator A reads it ( `version`=1) and deducts $50 from their account balance ($100-$50).
1. During Operator A's operation, Operator B also reads this
