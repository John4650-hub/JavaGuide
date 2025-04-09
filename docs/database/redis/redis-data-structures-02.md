I don't...
Title: Redis 3 Detailed Special Data Types\
Category: Database\
Tag:

- Redis\
  Head:
- Meta
- Name: keywords\
  Contact: Redis Common Data Type
- Meta
- Name: description\
  Content: Redis special data type summary: HyperLogLogs (basic statistics), Bitmap (bit storage), Geospatial (geographical location).\
  I don't...

In addition to the five basic data types, Redis supports three special data types: Bitmap, HyperLogLog, GEO.

# Bitmap (bit map)

# Introduction

According to the official documentation:

Bitmaps are not an actual data type, but a set of bit-oriented operations defined on the string type which is treated like a bit vector.

> Bitmap is not the actual data type in Redis, but a set of position-oriented operations defined in the string type as a bit vector. Because the string is a binary secure block with a maximum length of 512 MB, it is suitable for setting up to 2^32 different bits.

Bitmap stores consecutive binary numbers (0 and 1) and, by Bitmap, only one bit is needed to indicate the value or state of an element, and the key is the element itself. We know that eight bits can make up a byte, so Bitmap itself will save a lot of storage space.

You can see Bitmap as an array for the storage of binary numbers (0 and 1), where each element is named "offset".

![Bitmap](https://oss.javaguide.cn/github/javaguide/database/redis/image202071954133.png)

Common commands

| Orders                                | Introduction                                                      |
| ------------------------------------- | ----------------------------------------------------------------- |
| SETBIT key offset value               | Set the bit at offset to value                                    |
| GETBIT key offset                     | Gets the value of the given offset position                       |
| BITCOUNT key [start end]              | Counts the number of bits set to 1 between start and end          |
| BITOP operation destkey key1 key2 ... | Performs one or more Bitmap operations with AND, OR, XOR, and NOT |

**Bitmap Basic Operation Demonstration**:

```bash
# SETBIT returns the value of the previous position (default 0) where 7 bits are generated
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
# The number of bits that are set to 1 through BITCOUNT.
> BITCOUNT mykey
(integer) 2
```

Apply scene

**Situation where status information needs to be saved (as indicated by 0/1)**

- Examples: user sign-ups, active users, user behavior statistics (e.g., whether a video has been praised).
- Related commands: `SETBIT`, `GETBIT`, `BITCOUNT`, `BITOP`.

# HyperLogLog

# Introduction

HyperLogLog is a reputable probabilistic algorithm based on the LogLog Counting (LLC) optimization, not unique to Redis, but achieved it and provided some open-box API.

The HyperLogLog space available in Redis is very small, and only 12k space is needed to store close to `2^64` elements. That's really good. That's math. Moreover, Redis optimized the storage structure for HyperLogLog in two ways:

- **Rare matrix**: very small space is occupied when the number is small.
- **Streak matrix**: 12k space is occupied when the number reaches a certain threshold.

Redis has a detailed description of this:

![HyperLogLog](https://oss.javaguide.cn/github/javaguide/database/redis/image2027210914563.png)

The probabilistic algorithm for counting does not store metadata directly in order to save memory, but estimates the base value (number of elements in the set) by means of a certain probability statistical method. Therefore, HyperLogLog's count results are not an exact value and there is some error (the standard error is `0.81%`).

![HyperLogLog](https://oss.javaguide.cn/github/javaguide/database/redis/image202071954133.png)

The use of HyperLogLog is simple, but it's complicated. The principles of HyperLogLog and its realization in Redis can be seen in this article: [HyperLogLog algorithms' rationale and how Redis applies it](https://juejin.cn/post/6844903785744056333).

A tool to help understand the principles of HyperLogLog: [Sketch of the Day: HyperLogLog - Cornerstone of a Big Data Institute](http://content.research.neustar.biz/blog/hll.html).

In addition to HyperLogLog, Redis provides other probabilistic data structures corresponding to the official document address: <https://redis.io/docs/data-types/probabilistic/>.

Common commands

Hyper
