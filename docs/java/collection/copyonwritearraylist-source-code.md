---
title: CopyOnWriteArrayList Source Code Analysis
category: Java
tag:
  - Java Collections
---

## Introduction to CopyOnWriteArrayList

Before JDK 1.5, if you wanted to use a concurrent-safe `List`, the only option was `Vector`. However, `Vector` is an outdated collection that has been deprecated. `Vector` adds `synchronized` to almost all methods such as add, remove, update, and query. While this approach ensures synchronization, it effectively applies a large lock to the entire `Vector`, causing every method execution to acquire the lock, resulting in very low performance.

JDK 1.5 introduced the `Java.util.concurrent` (JUC) package, which provides many thread-safe containers with good concurrent performance. The only thread-safe `List` implementation among them is `CopyOnWriteArrayList`. For a summary of common concurrent containers in the `java.util.concurrent` package, you can refer to my article: [Summary of Common Java Concurrent Containers](https://javaguide.cn/java/concurrent/java-concurrent-collections.html).

### What Makes CopyOnWriteArrayList So Powerful?

In most business scenarios, read operations are often far greater than write operations. Since read operations do not modify the original data, locking for every read is actually a waste of resources. In contrast, we should allow multiple threads to access the internal data of the `List` simultaneously, as it is safe for read operations.

This idea is very similar to the design philosophy of `ReentrantReadWriteLock`, which allows for read-read non-exclusivity, read-write exclusivity, and write-write exclusivity (only read-read is non-exclusive). `CopyOnWriteArrayList` takes this idea a step further. To maximize the performance of read operations, `CopyOnWriteArrayList` does not require locking for reading. Even more impressively, write operations do not block read operations; only write-write operations are mutually exclusive. This significantly enhances the performance of read operations.

The thread safety of `CopyOnWriteArrayList` is fundamentally based on its use of the **Copy-On-Write** strategy, as indicated by its name.

### What is the Copy-On-Write Concept?

The "Copy-On-Write" in `CopyOnWriteArrayList` refers to the strategy of writing by copying, abbreviated as COW.

Here is a good introduction to Copy-On-Write from Wikipedia:

> Copy-on-write (COW) is an optimization strategy in computer programming. The core idea is that if multiple callers request the same resource (such as memory or data storage on disk) simultaneously, they will share the same pointer to the same resource until one caller attempts to modify the content of the resource. At that point, the system will create a private copy for that caller, while other callers continue to see the original resource unchanged. This process is transparent to other callers. The main advantage of this approach is that if a caller does not modify the resource, no private copy is created, allowing multiple callers to share the same resource during read operations.

Using `CopyOnWriteArrayList` as an example: when modifying the contents of `CopyOnWriteArrayList` (such as `add`, `set`, `remove`, etc.), it does not directly modify the original array. Instead, it first creates a copy of the underlying array, modifies the copy, and then assigns the modified array back. This ensures that write operations do not affect read operations.

It is clear that the copy-on-write mechanism is very suitable for concurrent scenarios with many reads and few writes, greatly improving the system's concurrent performance.

However, the copy-on-write mechanism is not a silver bullet; it still has some drawbacks. Here are a few:

1. Memory Usage: Each write operation requires a copy of the original data, which consumes additional memory. In cases with large data volumes, this may lead to insufficient memory resources.
1. Write Operation Overhead: Each write operation requires copying the original data, followed by modification and replacement, resulting in relatively high overhead for write operations. In scenarios with frequent writes, performance may be affected.
1. Data Consistency Issues: Modification operations do not immediately reflect in the final result and must wait for the copy to complete, which may lead to certain data consistency issues.
1. ……

## Source Code Analysis of CopyOnWriteArrayList

Here, we will analyze the core source code of `CopyOnWriteArrayList` using JDK 1.8 as an example.

The class definition of `CopyOnWriteArrayList` is as follows:

```java
public class CopyOnWriteArrayList<E>
extends Object
implements List<E>, RandomAccess, Cloneable, Serializable
{
  //...
}
```

`CopyOnWriteArrayList` implements the following interfaces:

- `List`: Indicates that it is a list that supports operations such as adding, removing, and searching, and can be accessed by index.
- `RandomAccess`: This is a marker interface indicating that the `List`
