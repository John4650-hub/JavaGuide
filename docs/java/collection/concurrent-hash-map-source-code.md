---
title: Analysis of ConcurrentHashMap Source Code
category: Java
tag:
  - Java Collections
---

> This article is from the WeChat public account: Submission from Mo Du Code, original address: <https://mp.weixin.qq.com/s/AHWzboztt53ZfFZmsSnMSw>.

The previous article introduced the source code of HashMap, which received a good response, and many students shared their views. This time, we are here again, this time it's `ConcurrentHashMap`. As a thread-safe HashMap, it is also used frequently. So what is its storage structure and implementation principle?

## 1. ConcurrentHashMap 1.7

### 1. Storage Structure

![Java 7 ConcurrentHashMap Storage Structure](https://oss.javaguide.cn/github/javaguide/java/collection/java7_concurrenthashmap.png)

The storage structure of `ConcurrentHashMap` in Java 7 is shown in the figure above. `ConcurrentHashMap` is composed of many `Segments`, and each `Segment` is a structure similar to `HashMap`, so each `HashMap` can be expanded internally. However, the number of `Segments` cannot be changed once it is **initialized**. The default number of `Segments` is 16, which means you can think of `ConcurrentHashMap` as supporting up to 16 threads concurrently by default.

### 2. Initialization

Let's explore the initialization process of `ConcurrentHashMap` through its no-argument constructor.

```java
    /**
     * Creates a new, empty map with a default initial capacity (16),
     * load factor (0.75) and concurrencyLevel (16).
     */
    public ConcurrentHashMap() {
        this(DEFAULT_INITIAL_CAPACITY, DEFAULT_LOAD_FACTOR, DEFAULT_CONCURRENCY_LEVEL);
    }
```

The no-argument constructor calls the parameterized constructor, passing in the default values for the three parameters, which are:

```java
    /**
     * Default initial capacity
     */
    static final int DEFAULT_INITIAL_CAPACITY = 16;

    /**
     * Default load factor
     */
    static final float DEFAULT_LOAD_FACTOR = 0.75f;

    /**
     * Default concurrency level
     */
    static final int DEFAULT_CONCURRENCY_LEVEL = 16;
```

Next, let's look at the internal implementation logic of this parameterized constructor.

```java
@SuppressWarnings("unchecked")
public ConcurrentHashMap(int initialCapacity,float loadFactor, int concurrencyLevel) {
    // Parameter validation
    if (!(loadFactor > 0) || initialCapacity < 0 || concurrencyLevel <= 0)
        throw new IllegalArgumentException();
    // Validate concurrency level size, reset to 65536 if greater than 1<<16
    if (concurrencyLevel > MAX_SEGMENTS)
        concurrencyLevel = MAX_SEGMENTS;
    // Find power-of-two sizes best matching arguments
    // Power of 2
    int sshift = 0;
    int ssize = 1;
    // This loop finds the nearest power of 2 above concurrencyLevel
    while (ssize < concurrencyLevel) {
        ++sshift;
        ssize <<= 1;
    }
    // Record segment shift
    this.segmentShift = 32 - sshift;
    // Record segment mask
    this.segmentMask = ssize - 1;
    // Set capacity
    if (initialCapacity > MAXIMUM_CAPACITY)
        initialCapacity = MAXIMUM_CAPACITY;
    // c = capacity / ssize, default 16 / 16 = 1, this calculates the capacity similar to HashMap in each Segment
    int c = initialCapacity / ssize;
    if (c * ssize < initialCapacity)
        ++c;
    int cap = MIN_SEGMENT_TABLE_CAPACITY;
    // The capacity in Segment similar to HashMap must be at least 2 or a multiple of 2
    while (cap < c)
        cap <<= 1;
    // create segments and segments[0]
    // Create Segment array, set segments[0]
    Segment<K,V> s0 = new Segment<K,V>(loadFactor, (int)(cap * loadFactor),
                         (HashEntry<K,V>[])new HashEntry[cap]);
    Segment<K,V>[] ss = (Segment<K,V>[])new Segment[ssize];
    UNSAFE.putOrderedObject(ss, SBASE, s0); // ordered write of segments[0]
    this.segments = ss;
}
```

To summarize the initialization logic of `ConcurrentHashMap` in Java 7:

1. Necessary parameter validation.
1. Validate the size of the concurrency level `concurrencyLevel`, resetting to the maximum value if it exceeds the maximum. The default value for the no-argument constructor is **16**.
1. Find the nearest **power of 2** above the concurrency level `concurrencyLevel` as the initial capacity, which is **16** by default.
   4
