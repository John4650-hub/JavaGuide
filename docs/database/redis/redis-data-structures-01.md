---
title: Detailed Explanation of 5 Basic Data Types in Redis
category: Database
tag:
  - Redis
head:
  - - meta
    - name: keywords
      content: Common data types in Redis
  - - meta
    - name: description
      content: Summary of Redis basic data types: String, List, Set, Hash, Zset
---

Redis has a total of 5 basic data types: String, List, Set, Hash, and Zset.

These 5 data types are directly provided for user use and represent the form of data storage. Their underlying implementation mainly relies on these 8 data structures: Simple Dynamic Strings (SDS), LinkedList, Dict (hash table/dictionary), SkipList, Intset (integer set), ZipList (compressed list), and QuickList.

The underlying data structure implementations corresponding to the 5 basic data types in Redis are shown in the table below:

| String | List                         | Hash          | Set          | Zset              |
| :----- | :--------------------------- | :------------ | :----------- | :---------------- |
| SDS    | LinkedList/ZipList/QuickList | Dict, ZipList | Dict, Intset | ZipList, SkipList |

Before Redis 3.2, the underlying implementation of List was LinkedList or ZipList. After Redis 3.2, QuickList, a combination of LinkedList and ZipList, was introduced, and the underlying implementation of List changed to QuickList. Starting from Redis 7.0, ZipList has been replaced by ListPack.

You can find a very detailed introduction to Redis data types/structures on the official Redis website:

- [Redis Data Structures](https://redis.com/redis-enterprise/data-structures/)
- [Redis Data types tutorial](https://redis.io/docs/manual/data-types/data-types-tutorial/)

In the future, with the release of new versions of Redis, new data structures may appear. By consulting the corresponding introductions on the Redis official website, you can always obtain the most reliable information.

![](https://oss.javaguide.cn/github/javaguide/database/redis/image-20220720181630203.png)

## String

### Introduction

String is the simplest and most commonly used data type in Redis.

String is a binary-safe data type that can be used to store any type of data, such as strings, integers, floating-point numbers, images (base64 encoding or decoding of images or image paths), and serialized objects.

![](https://oss.javaguide.cn/github/javaguide/database/redis/image-20220719124403897.png)

Although Redis is written in C, it does not use C's string representation but instead constructs a **Simple Dynamic String** (SDS). Compared to C's native strings, Redis's SDS can not only store text data but also binary data, and the complexity of obtaining the string length is O(1) (C strings are O(N)). In addition, Redis's SDS API is safe and does not cause buffer overflows.

### Common Commands

| Command                          | Description                                         |
| -------------------------------- | --------------------------------------------------- |
| SET key value                    | Set the value of the specified key                  |
| SETNX key value                  | Set the value of the key only if it does not exist  |
| GET key                          | Get the value of the specified key                  |
| MSET key1 value1 key2 value2 ... | Set the values of one or more specified keys        |
| MGET key1 key2 ...               | Get the values of one or more specified keys        |
| STRLEN key                       | Return the length of the string value stored at key |
| INCR key                         | Increment the numeric value stored at key by one    |
| DECR key                         | Decrement the numeric value stored at key by one    |
| EXISTS key                       | Determine if the specified key exists               |
| DEL key (general)                | Delete the specified key                            |
| EXPIRE key seconds (general)     | Set an expiration time for the specified key        |

For more Redis String commands and detailed usage guides, please refer to the corresponding introduction on the Redis official website: <https://redis.io/commands/?group=string>.

**Basic Operations**:

```bash
> SET key value
OK
> GET key
"value"
> EXISTS key
(integer) 1
> STRLEN key
(integer) 5
> DEL key
(integer) 1
> GET key
(nil)
```

**Batch Setting**:

```bash
> MSET key1 value1 key2 value2
OK
> MGET key1 key2 # Batch get the values corresponding to multiple keys
1) "value1"
2) "value2"
```

**Counter (when the content of the string is an integer)**:

```bash
> SET number 1
OK
> INCR number # Increment the numeric value stored at key by one
(integer) 2
> GET number
```
