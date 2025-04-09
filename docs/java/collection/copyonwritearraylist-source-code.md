I don't...
type: CopyOnWriteArrayList source analysis
Category:
Tag:

- Java.
  I don't...

# CopyOnWriteArrayList

Before JDK 1.5, only `Vector` could be selected if you wanted to use a `List` that is safe. `Vector` is an old collection that has been eliminated. `Vector` has largely added `synchronized` to methods such as additions, deletions, etc., which, while ensuring synchronization, amounts to a large lock on the whole `Vector`, so that each method is implemented with locks, resulting in very low performance.

In JDK 1.5, the `Java.util.concurrent` (JUC) package was introduced, which provides many thread-safe and concurrent collections, of which the only thread-safe `List` is `CopyOnWriteArrayList`. For a summary of common concurrent collections under the `java.util.concurrent` package, see the article I wrote: [Java Common Concurrent Collections Summary](https://javaguide.cn/java/concurrent/java-concurrent-collections.html).

# CopyOnWriteArrayList

For most business scenarios, reading operations are often much larger than writing operations. As the reading operation does not modify the original data, it is a waste of resources to lock each reading. By contrast, we should allow multiple threads to access the `List` internal data at the same time, which is, after all, safe for reading operations.

This approach is very similar to the design of the `ReentrantReadWriteLock` reading and writing locks, i.e., reading and reading and writing and writing (read and read and write). This idea was further realized by `CopyOnWriteArrayList`. In order to achieve maximum reading performance, the reading in `CopyOnWriteArrayList` is completely unlocked. Even more so, writing does not block reading, and writing is the only operation that can interfere with reading. So, the reading operation can be significantly improved.

The core of the `CopyOnWriteArrayList` thread safety lies in its use of the **Copy-On-Write** strategy, as can be seen from the name `CopyOnWriteArrayList`.

# Copy-On-Write

“Copy-On-Write” in the name `CopyOnWriteArrayList` is abbreviated as COW.

Here's a good presentation by Wikipedia on Copy-On-Write:

> Copying at writing (English: Copy-on-write, short COW) is an optimization strategy in the area of computer program design. The core idea is that if there are multiple callers who request the same resources at the same time (e.g., memory or data storage on disk), they will jointly access the same pointer to the same resources, and the system will not actually copy a specific copy of the resource to the caller until a caller tries to modify the content of the resource, while the initial resources seen by other callers remain unchanged. This process is transparent to other callers. The main advantage of this approach is that if the caller does not modify the resource, no copy (private copy) will be created, so that multiple callers can share the same resource when reading the operation.

The example of `CopyOnWriteArrayList` is presented here: when the content of `CopyOnWriteArrayList` needs to be modified (`add`, `set`, `remove`, etc.), `CopyOnWriteArrayList` does not directly modify the original array, but a copy of the underlying array is created, changes are made to the copy, and then the modified array is returned, so that the writing operation does not affect the reading operation.

As can be seen, the copying mechanism is well suited to read and write less, and can significantly improve the performance of the system.

However, the copying mechanism is not a silver bullet, and there are still some shortcomings:

1. Memory occupation: Each writing operation requires a copy of the original data, taking up additional memory space and, in the case of larger volumes, may result in insufficient memory resources.
1. Cost of writing operations: Each writing operation requires a copy of the original data, which is then modified and replaced, so the cost of writing operations is relatively high and, in the case of more frequent writing, performance may be affected.
1. Data consistency issues: Modifications will not be immediately reflected in the final results and will have to wait for replication to be completed, which may lead to some data consistency issues.
1. ...

# CopyOnWriteArrayList Source Analysis

Using the example of JDK 1.8, the core source code of `CopyOnWriteArrayList` is analyzed.

The class `CopyOnWriteArrayList` is defined as follows:

```java
public class CopyOnWriteArrayList extends Object {
    // Other members...
}
```

`CopyOnWriteArrayList` has achieved the following interfaces:

- `List`: Shows that it is a list that supports additions, deletions, searches, etc., and can be accessed by index.
- `RandomAccess`: This is a marker interface that shows that
