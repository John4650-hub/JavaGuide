---
title: Summary of Common Redis Interview Questions (Part 1)
category: Database
tag:
  - Redis
head:
  -   - meta
      - name: keywords
        content: Redis Basics, Common Redis Data Structures, Redis Thread Model, Redis Memory Management, Redis Transactions, Redis Performance Optimization
  -   - meta
      - name: description
        content: An article summarizing common knowledge points and interview questions about Redis, covering Redis basics, common data structures, thread model, memory management, transactions, performance optimization, and more.
---

<!-- @include: @small-advertisement.snippet.md -->

## Redis Basics

### What is Redis?

[Redis](https://redis.io/) (**RE**mote **DI**ctionary **S**erver) is an open-source NoSQL database developed in C language (BSD license). Unlike traditional databases, Redis stores data in memory (in-memory database, supports persistence), which allows for very fast read and write speeds, making it widely used in distributed caching. Additionally, Redis stores data as key-value pairs (KV).

To meet different business scenarios, Redis has built-in implementations of various data types (such as String, Hash, Sorted Set, Bitmap, HyperLogLog, GEO). Furthermore, Redis supports transactions, persistence, Lua scripting, a publish-subscribe model, and various out-of-the-box clustering solutions (Redis Sentinel, Redis Cluster).

![Overview of Redis Data Types](https://oss.javaguide.cn/github/javaguide/database/redis/redis-overview-of-data-types-2023-09-28.jpg)

Redis has no external dependencies, and Linux and OS X are the two operating systems where Redis is developed and tested the most. The official recommendation is to deploy Redis in a production environment using Linux.

For personal learning, you can install Redis on your local machine or use the [online Redis environment](https://try.redis.io/) provided by the Redis official website (some commands may not be available) to experience Redis in practice.

![try-redis](https://oss.javaguide.cn/github/javaguide/database/redis/try.redis.io.png)

Many websites around the world use Redis, and [techstacks.io](https://techstacks.io/) maintains a [list of popular sites using Redis](https://techstacks.io/tech/redis). If you're interested, you can take a look.

### Why is Redis so fast?

Redis has implemented many performance optimizations internally, with the following four points being particularly important:

1. **Memory-Based Storage**: This is the primary reason. Redis read and write operations occur in memory, with access speeds at the nanosecond level, while traditional databases frequently read and write to disk at the millisecond level, resulting in several orders of magnitude difference.
1. **Efficient I/O Model (I/O Multiplexing & Single-Threaded Event Loop)**: Redis uses a single-threaded event loop combined with I/O multiplexing technology, allowing a single thread to handle multiple I/O events (such as read and write) on multiple network connections simultaneously, avoiding context switching and lock contention issues in multi-threaded models. Although it is single-threaded, the efficiency of memory operations and I/O multiplexing allows Redis to easily handle a large number of concurrent requests (the Redis thread model will be introduced in detail later).
1. **Optimized Internal Data Structures**: Redis provides various data types (such as String, List, Hash, Set, Sorted Set, etc.), and its internal implementation uses highly optimized encoding methods (such as ziplist, quicklist, skiplist, hashtable, etc.). Redis dynamically selects the most suitable internal encoding based on data size and type to achieve the best balance between performance and space efficiency.
1. **Simple and Efficient Communication Protocol (Simple Protocol - RESP)**: Redis uses its own designed RESP (REdis Serialization Protocol) protocol. This protocol is simple to implement, has good parsing performance, and is binary safe. The serialization/deserialization overhead for communication between the client and server is minimal, which helps improve overall interaction speed.

> The image below summarizes this well, shared from [Why is Redis so fast?](https://twitter.com/alexxubyte/status/1498703822528544770).

![why-redis-so-fast](./images/why-redis-so-fast.png)

Since Redis is so fast, why not use it as the primary database? The main reason is that memory costs are too high, and the data persistence provided by Redis still carries the risk of data loss.

### Besides Redis, do you know other distributed caching solutions?

If asked this question in an interview, the interviewer mainly wants to see:

1. Whether your choice of Redis as a distributed caching solution was based on rigorous research and thought, or just because Redis is currently a "hot" technology.
1. Your breadth of knowledge in distributed caching technologies.

If you are familiar with other solutions and can explain why you ultimately chose Redis (even better!), this will significantly enhance your interview performance
