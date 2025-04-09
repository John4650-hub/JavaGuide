I don't...
Title: Summary of Common Interviews at Redis (up)\
Category: Database\
Tag:

- Redis\
  Head:
- Meta
- Name: keywords\
  Content: Redis Foundation, Common Redis Data Structure, Redis Thread Model, Redis Memory Management, Redis Matters, Redis Performance Optimization
- Meta
- Name: description\
  Content: An article summarizing the knowledge points and interview topics common to Redis, covering the Redis Foundation, the common Redis data structure, the Redis thread model, Redis memory management, Redis services, and Redis performance optimization.\
  I don't...

@include: @small-advertition.snippet.md-->

# Redis Foundation

What's Redis?

[https://redis.io/](**RE**mote**DI**ction**S**erver) is an open-source NoSQL database (BSD license) developed in C languages. Unlike traditional databases, Redis data is stored in memory (RAM database, supporting permanence) and therefore reads and writes very quickly and is widely used in distributed cache directions. And, Redis stores KV key to data.

In order to meet the different business scenarios, Redis has built in multiple data types (e.g., String, Hash, Sorted Set, Bitmap, HyperLogLog, GEO). Moreover, Redis supports services, durability, Lua scripts, the release of subscription models, and a variety of case-in-box cluster programs (Redis Sentinel, Redis Cluster).

An overview of [Redis data types](https://oss.javaguide.cn/github/javaguide/database/redis/redis-overview-of-data-types-2023-09-28.jpg)

Redis has no external dependencies; Linux and OS X are the two operating systems that Redis has developed and tested most, and the official recommendation is to use Linux deployment in the production environment.

If you study, you can actually experience Redis by installing Redis on your own or by making it available through the Redis Network of Officials.

[try-redis](https://oss.javaguide.cn/github/javaguide/database/redis/try.redis.io.png)

Redis, [techstacks.io](https://techstacks.io/) dedicated to the maintenance of a [list of hot sites using Redis](https://techstacks.io/tech/redis), can be viewed with interest.

# Redis, why so fast?

There are a lot of performance optimizations within Redis, and the following four points are more important:

1. Redis: Memory-based access is much faster than on disk;
1. A high-impact event management model based on Redis Reactor design, mainly one-way cycle of events and IO multi-routing (detailed after the Redis thread model);
1. Redis incorporates a wide range of optimized data types/structures, with high performance;
1. Redis communication agreements are simple and interpretive.

> This is a pretty good summary of the picture below, shared from [Why is Redis so fast?](https://twitter.com/lexxubyte/status/149878382528544770).

[why-redis-so-fast](./images/why-redis-so-fast.png)

So why don't you just use Redis as the main database? This is mainly due to the high cost of memory and the fact that there is still a risk that data will be lost for the sustainability of the data provided by Redis.

# Do you know any distributional cache options besides Redis?

If asked about this in the interview, the interviewer wanted to see:

1. In choosing Redis as a distributed cache option, are you after careful research and reflection, or is it just because Redis is the current “hot” technology?
1. The technical breadth of your distributed cache direction.

If you know the other options and can explain why Redis was finally chosen, it'll give you a lot of credit for your interview!

The following is a brief discussion of the common distributed cache technology selection.

If distributed caches are available, are there more old cards that are also used? **Memcached** and **Redis**. However, there are still projects that use **Memcached** to make a cache, which is directly **Redis**.

Memcached was the first time that distributed caches had started, more often than not. Later, with the development of Redis, the use of the stronger Redis has slowly shifted.

A number of large plants also open distribution high-performance KV storage databases similar to Redis, such as [**Tendis**](https://github.com/Tencent/Tendis). Tendis is based on the reputable open-source project [RocksDB](https://github.com/facebook/rocksdb) as a storage engine 100% compatible with the Redis protocol and Redis 4.0 all data models. On the comparison between Redis and Tendis, the official post was published: (https://mp.weixin.qq.com/s/MeYkfOIdnU6LYlsGb24KjQ), for a brief reference.

However, as can be seen from the Github submission records of the Tendis project, the Tendis open-source version has almost not been maintained and updated, together with its low level of
