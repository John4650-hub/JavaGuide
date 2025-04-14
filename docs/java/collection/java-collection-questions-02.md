---
title: Summary of Common Java Collection Interview Questions (Part 2)
category: Java
tag:
  - Java Collections
head:
  -   - meta
      - name: keywords
        content: HashMap, ConcurrentHashMap, Hashtable, List, Set
  -   - meta
      - name: description
        content: A summary of common knowledge points and interview questions about Java collections, hoping to be helpful to you!
---

<!-- @include: @article-header.snippet.md -->

## Map (Important)

### Differences Between HashMap and Hashtable

- **Thread Safety:** `HashMap` is not thread-safe, while `Hashtable` is thread-safe because most of its internal methods are synchronized. (If you need to ensure thread safety, use `ConcurrentHashMap`!)
- **Efficiency:** Due to thread safety issues, `HashMap` is generally more efficient than `Hashtable`. Additionally, `Hashtable` is largely obsolete and should not be used in code.
- **Support for Null Keys and Values:** `HashMap` can store null keys and values, but only one null key is allowed, while multiple null values can be stored. `Hashtable` does not allow null keys or values, and will throw a `NullPointerException` if attempted.
- **Initial Capacity and Growth Size Differences:** ① If no initial capacity is specified during creation, `Hashtable` defaults to an initial size of 11, and each subsequent growth increases the capacity to 2n+1. `HashMap` defaults to an initial size of 16, and each subsequent growth doubles the capacity. ② If an initial capacity is specified, `Hashtable` uses that size directly, while `HashMap` expands it to the next power of 2 (the `tableSizeFor()` method in `HashMap` ensures this, and the source code is provided below). This means `HashMap` always uses powers of 2 for the hash table size, which will be explained later.
- **Underlying Data Structure:** After JDK 1.8, `HashMap` has undergone significant changes in handling hash collisions. When the length of a linked list exceeds a threshold (default is 8), it converts the list into a red-black tree (before converting to a red-black tree, it checks if the current array length is less than 64; if so, it opts for array expansion instead of conversion to a red-black tree) to reduce search time (this process will be analyzed in conjunction with the source code later). `Hashtable` does not have such a mechanism.
- **Hash Function Implementation:** `HashMap` applies a high and low bit mixing disturbance to the hash value to reduce collisions, while `Hashtable` directly uses the key's `hashCode()` value.

**Constructor of `HashMap` with Initial Capacity:**

```java
    public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);
        this.loadFactor = loadFactor;
        this.threshold = tableSizeFor(initialCapacity);
    }
     public HashMap(int initialCapacity) {
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
    }
```

The following method ensures that `HashMap` always uses powers of 2 for the hash table size.

```java
/**
 * Returns a power of two size for the given target capacity.
 */
static final int tableSizeFor(int cap) {
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```

### Differences Between HashMap and HashSet

If you have looked at the source code of `HashSet`, you should know that `HashSet` is implemented based on `HashMap`. (The source code of `HashSet` is very minimal because, apart from `clone()`, `writeObject()`, and `readObject()`, which `HashSet` must implement, all other methods directly call methods from `HashMap`.)

|                `HashMap`                 |                     `HashSet`                     |
| :--------------------------------------: | :-----------------------------------------------: |
|        Implements `Map` interface        |            Implements `Set` interface             |
|          Stores key-value pairs          |                Stores only objects                |
| Calls `put()` to add elements to the map | Calls `add()` method to add elements to the `Set` |
|                \`HashMap                 |                                                   |
