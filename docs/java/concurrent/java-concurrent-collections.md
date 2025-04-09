I don't...
Title: Java Common Conveyance Summary
Category:
Tag:

- Java.
  I don't...

The bulk of the containers provided by JDK are in the `java.util.concurrent` package.

- **`ConcurrentHashMap`**: Line secure `HashMap`
- **`CopyOnWriteArrayList`**: Line secure `List`, which is far better than `Vector`, in the context of reading and writing less.
- **`ConcurrentLinkedQueue`**: Efficient side-by-side Queue, achieved using a chain chart. It can be seen as `LinkedList`, a non-stop queue.
- **`BlockingQueue`**: This is an interface that JDK has achieved internally through chains, arrays, etc. Expressed that blocking queues are well suited for use as a conduit for data sharing.
- **`ConcurrentSkipListMap`**: Realization of the Jump Table. This is a Map, which uses the tabulation data structure for quick search.

# ConcurrentHashMap

We know that `HashMap` is unsafe, and that, if used in the context of symmetry, a common solution is to package `HashMap` through `Collections.synchronizedMap()`, so that it becomes linear security. However, this approach synchronizes cross-routing access across different lines through a global lock, which can lead to serious performance bottlenecks, particularly in high-cost scenarios.

To address this problem, `ConcurrentHashMap` was created as a linear security version of `HashMap` which provides more efficient co-processing capabilities.

At JDK 1.7, `ConcurrentHashMap` splits the entire barrel array (`Segment`, locks), each lock only locks a portion of the container's data (the following graph) and allows multiple-thread access to data from different data segments in the container, and there is no lock competition to increase access.

![Java7 ConcurrentHashMap Storage Structure](https://oss.javaguide.cn/github/javaguide/java/collection/java7_concurrenthashmap.png)

By the time JDK 1.8, `ConcurrentHashMap` removed the `Segment` sub-lock and used `Node + CAS + synchronized` to ensure its safety. The data structure is similar to that of `HashMap` 1.8, array + chain table / red and black fork tree. Java 8 converts a chain table (O(N)) to a black tree (O(log(N))) when the length of the chain exceeds a certain threshold (8).

In Java 8, the lock particle size is even finer, `synchronized` locks only on the front nodes of the current chain or the red and black fork tree so that, as long as the Hash does not conflict, it does not produce contention and does not affect the reading and writing of other Nodes, and is significantly more efficient.

![Java8 ConcurrentHashMap Storage Structure](https://oss.javaguide.cn/github/javaguide/java/collection/java8_concurrenthashmap.png)

For a detailed description of `ConcurrentHashMap`, please see the article I wrote: [`ConcurrentHashMap Source Analysis`](..............).

# CopyOnWriteArrayList

Before JDK 1.5, only `Vector` could be selected if you wanted to use a `List` that is safe. `Vector` is an old collection that has been eliminated. `Vector` has largely added `synchronized` to methods such as additions, deletions, etc., which, while ensuring synchronization, amounts to a large lock on the whole `Vector`, so that each method is implemented with locks, resulting in very low performance.

In JDK 1.5, the `Java.util.concurrent` (JUC) package was introduced, which provides many line-safe and co-operative packagings, of which the only linear security `List` is `CopyOnWriteArrayList`.

For most business scenarios, reading operations are often much larger than writing operations. As the reading operation does not modify the original data, it is a waste of resources to lock each reading. By contrast, we should allow multiple threads to access `List` internal data at the same time, which is, after all, safe for reading operations.

This approach is very similar to the design of the `ReentrantReadWriteLock` reading and writing locks, i.e., reading and reading and writing and writing (read and read and write). This idea was further realized by `CopyOnWriteArrayList`. In order to achieve maximum reading performance, the reading in `CopyOnWriteArrayList` is completely unlocked. Even more so, writing does not block reading, and writing is the only way to repulse each other. So, the reading operation can be significantly improved.

The core of the `CopyOnWriteArrayList` thread safety lies in its use of the **Copy-On-Write** strategy, as can be seen from the name `CopyOnWriteArrayList`.

When changes are required (`add`, `set`, `remove`, etc.) in `CopyOnWriteArrayList`, the original
