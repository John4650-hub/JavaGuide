---
title: Detailed Explanation of 3 Special Data Types in Redis
category: Database
tag:
  - Redis
head:
  - - meta
    - name: keywords
      content: Common data types in Redis
  - - meta
    - name: description
      content: Summary of Redis special data types: HyperLogLogs (cardinality statistics), Bitmap (bit storage), Geospatial (geolocation).
---

In addition to the 5 basic data types, Redis also supports 3 special data types: Bitmap, HyperLogLog, and GEO.

## Bitmap

### Introduction

According to the official website:

> Bitmaps are not an actual data type, but a set of bit-oriented operations defined on the String type which is treated like a bit vector. Since strings are binary safe blobs and their maximum length is 512 MB, they are suitable to set up to 2^32 different bits.
>
> Bitmap is not an actual data type in Redis, but a set of bit-oriented operations defined on the String type, treated as a bit vector. Since strings are binary safe blobs and their maximum length is 512 MB, they are suitable for setting up to 2^32 different bits.

Bitmaps store continuous binary numbers (0 and 1). With Bitmap, only one bit is needed to represent the value or state of a corresponding element, where the key is the element itself. We know that 8 bits can form one byte, so Bitmap can greatly save storage space.

You can think of Bitmap as an array that stores binary numbers (0 and 1), where each element's index is called an offset.

![](https://oss.javaguide.cn/github/javaguide/database/redis/image-20220720194154133.png)

### Common Commands

| Command                               | Description                                                                     |
| ------------------------------------- | ------------------------------------------------------------------------------- |
| SETBIT key offset value               | Set the value at the specified offset position                                  |
| GETBIT key offset                     | Get the value at the specified offset position                                  |
| BITCOUNT key start end                | Get the number of elements with a value of 1 between start and end              |
| BITOP operation destkey key1 key2 ... | Perform operations on one or more Bitmaps, with operators AND, OR, XOR, and NOT |

**Basic Bitmap Operations Demonstration**:

```bash
# SETBIT will return the previous value of the bit (default is 0), generating 7 bits
> SETBIT mykey 7 1
(integer) 0
> SETBIT mykey 7 0
(integer) 1
> GETBIT mykey 7
(integer) 0
> SETBIT mykey 6 1
(integer) 0
> SETBIT mykey 8 1
(integer) 0
# Count the number of bits set to 1 using bitcount.
> BITCOUNT mykey
(integer) 2
```

### Application Scenarios

**Scenarios that require saving state information (0/1 can represent)**

- Example: User sign-in status, active user status, user behavior statistics (e.g., whether a user has liked a certain video).
- Related commands: `SETBIT`, `GETBIT`, `BITCOUNT`, `BITOP`.

## HyperLogLog

### Introduction

HyperLogLog is a well-known probabilistic algorithm for cardinality counting, optimized from LogLog Counting (LLC). It is not unique to Redis; Redis simply implements this algorithm and provides some out-of-the-box APIs.

The HyperLogLog provided by Redis occupies a very small space, requiring only 12k of space to store nearly `2^64` different elements. This is truly impressive; this is the charm of mathematics! Additionally, Redis has optimized the storage structure of HyperLogLog, using two counting methods:

- **Sparse Matrix**: When the count is low, it occupies very little space.
- **Dense Matrix**: When the count reaches a certain threshold, it occupies 12k of space.

The official Redis documentation provides detailed explanations:

![](https://oss.javaguide.cn/github/javaguide/database/redis/image-20220721091424563.png)

The cardinality counting probabilistic algorithm does not directly store metadata to save memory; instead, it estimates the cardinality value (the number of elements in the set) through certain probabilistic statistical methods. Therefore, the counting result of HyperLogLog is not an exact value and has a certain margin of error (standard error is `0.81%`).

![](https://oss.javaguide.cn/github/javaguide/database/redis/image-20220720194154133.png)

Using HyperLogLog is very simple, but the principle is quite complex. You can read this article for an explanation of the principle of HyperLogLog and how it is applied in Redis: \[Explanation of the Principle of HyperLogLog Algorithm and How Redis Applies It\](https://juejin.cn/post/684490378574
