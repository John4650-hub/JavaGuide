I don't...
title: ConcurrentHashMap Source Analysis
Category:
Tag:

- Java.
  I don't...

> This submission is from the Public Code: Last-reading code, original address: <https://mp.weixin.qq.com/s/AHWzboztt53ZfZmsSnMSw>.

The previous article, which described the HashMap source code, had a good resonance, and there were many students who had expressed their views, and this time again `Concurrent HashMap`, which was also used very frequently as a secure thread. So what about its storage structure and realization?

# 1. Concurrent HashMap 1.7

## 1. Storage Structure

[Java 7 ConcurrentHashMap Storage Structure](https://oss.javaguide.cn/github/javaguide/java/collection/java7_concurrenthashmap.png)

The storage structure of `Concurrent HashMap` in Java 7 is as shown above. `Concurrent HashMap` is a combination of many `Segment`, and each `Segment` is a structure similar to `HashMap`, so each `HashMap` can be expanded internally. However, the number of `Segment` cannot be changed once it has been initialized; the default number of `Segment` is 16, and you can also consider `Concurrent HashMap` as the default support of up to 16 threads.

## 2. Initialization

Search for the initialization process of `ConcurrentHashMap` through the unincorporated construction of `ConcurrentHashMap`.

```Java
/**
 * Creates a new, empty Map with a special capacity (16),
 * load factor (0.75) and concurrency level (16).
 */
public ConcurrentHashMap() {
    this(DEFAULT_INITIAL_CAPACITY, DEFAULT_LOAD_FACTOR, DEFAULT_CONCURRENCY_LEVEL);
}
```

The default value of three parameters, the values of which are, was used in the non-partition structure.

```Java
/**
 * Default initialization capacity
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

Then look at this internal realization logic with a nuclei function.

```Java
@SuppressWarnings
public ConcurrentHashMap(
    // Parameter Verification
    if (!(loadFactor > 0) || initialCapacity < 0 || concurrencyLevel <= 0) {
        throw new IllegalArgumentException();
    }
    // Validate concurrency level size greater than 1 < 16 and reset to 65536
    if (concurrencyLevel > MAX_SEGMENTS) {
        concurrencyLevel = MAX_SEGMENTS;
    }
    // Find Power-of-Two sizes best matching arms
    int ssize = 1;
    // This cycle can find the nearest 2 subsquare values above concurrencyLevel
    while (ssize < concurrencyLevel) {
        ssize <<= 1;
    }
    // Record segment offset
    this.segmentShift = 32 - shift;
    // Record segment mask
    this.segmentMask = ssize - 1;
    // Set Capacity
    if (initialCapacity > MAXIMUM_CAPACITY) {
        initialCapacity = MAXIMUM_CAPACITY;
    }
    // c = capacity / ssize, default 16 / 16 = 1, which calculates the capacity similar to HashMap in each Segment
    int c = initialCapacity / ssize;
    if (c * ssize < initialCapacity) {
        ++c;
    }
    int cap = MIN_SEGMENT_TABLE_CAPACITY;
    // HashMap-like capacity of at least 2 or 2 in Segment
    while (cap < c) {
        cap <<= 1;
    }
    // Create plans and plans [0]
    // Create Segment arrays, set segments [0]
    Segment<K, V> s0 = new Segment<K, V>(loadFactor, (HashEntry<K, V>[]) new HashEntry[cap]);
    Segment<K, V>[] ss = (Segment<K, V>[]) new Segment[ssize];
    UNSAFE.putOrderedObject(ss, SBASE, s0); // ordered write of segment[0]
    this.segments = ss;
}
```

Summarizes the initialization logic of Concurrent HashMap in Java 7.

1. Validation of necessary parameters.
1. Validation of the `concurrencyLevel` size at the same level and reset to the maximum if greater. Unparticipated **Default value is 16.**
1. Search for the most recent \*\*sub-value above `concurrencyLevel` as an initialized capacity size, **default 16**.
1. Record the `segmentShift` offset, which is N of [capacity = 2 next to N], which is used to calculate position at the back of Put. **Default is 32 - Shift = 28**.
1. Record `segmentMask`, default is ssize - 1 = 16 - 1 = 15.
1.
