---
title: Detailed Explanation of Redis Memory Fragmentation
category: Database
tag:
  - Redis
---

## What is Memory Fragmentation?

You can simply understand memory fragmentation as the unusable free memory.

For example, if the operating system allocates a continuous memory space of 32 bytes for you, but you only need to use 24 bytes of that space to store data, then the extra 8 bytes of memory, if it cannot be allocated for other data later, can be referred to as memory fragmentation.

![Memory Fragmentation](https://oss.javaguide.cn/github/javaguide/memory-fragmentation.png)

Although Redis memory fragmentation does not affect Redis performance, it does increase memory consumption.

## Why Does Redis Memory Fragmentation Occur?

There are two common reasons for Redis memory fragmentation:

**1. The memory space requested by Redis from the operating system when storing data may be larger than the actual storage space needed.**

Here is the original statement from the Redis official documentation:

> To store user keys, Redis allocates at most as much memory as the `maxmemory` setting enables (however there are small extra allocations possible).

When Redis allocates memory using the `zmalloc` method (a memory allocation method implemented by Redis itself), it not only allocates memory of size `size`, but also allocates additional memory of size `PREFIX_SIZE`.

The source code for the `zmalloc` method is as follows (source link: <https://github.com/antirez/redis-tools/blob/master/zmalloc.c>):

```java
void *zmalloc(size_t size) {
   // Allocate memory of the specified size
   void *ptr = malloc(size+PREFIX_SIZE);
   if (!ptr) zmalloc_oom_handler(size);
#ifdef HAVE_MALLOC_SIZE
   update_zmalloc_stat_alloc(zmalloc_size(ptr));
   return ptr;
#else
   *((size_t*)ptr) = size;
   update_zmalloc_stat_alloc(size+PREFIX_SIZE);
   return (char*)ptr+PREFIX_SIZE;
#endif
}
```

Additionally, Redis can use various memory allocators to allocate memory (libc, jemalloc, tcmalloc), with the default being [jemalloc](https://github.com/jemalloc/jemalloc), which allocates memory based on a series of fixed sizes (8 bytes, 16 bytes, 32 bytes, etc.). The memory units allocated by jemalloc are shown in the following diagram:

![jemalloc Memory Unit Diagram](https://oss.javaguide.cn/github/javaguide/database/redis/6803d3929e3e46c1b1c9d0bb9ee8e717.png)

When the memory requested by the program is closest to a fixed value, jemalloc allocates the corresponding size of space. For example, if the program requests 17 bytes of memory, jemalloc will allocate 32 bytes, resulting in a waste of 15 bytes of memory. However, jemalloc has been optimized specifically for memory fragmentation issues, so excessive fragmentation is generally not a problem.

**2. Frequent modifications to data in Redis can also lead to memory fragmentation.**

When a piece of data in Redis is deleted, Redis typically does not readily release memory back to the operating system.

This is also mentioned in the official Redis documentation:

![](https://oss.javaguide.cn/github/javaguide/redis-docs-memory-optimization.png)

Documentation link: <https://redis.io/topics/memory-optimization>.

## How to Check Redis Memory Fragmentation Information?

You can use the `info memory` command to view Redis memory-related information. The specific meanings of each parameter in the following image are detailed in the Redis official documentation: <https://redis.io/commands/INFO>.

![](https://oss.javaguide.cn/github/javaguide/redis-info-memory.png)

The formula for calculating Redis memory fragmentation ratio: `mem_fragmentation_ratio` (memory fragmentation ratio) = `used_memory_rss` (the actual physical memory space allocated to Redis by the operating system) / `used_memory` (the actual memory space requested by the Redis memory allocator for storing data).

In other words, a larger value of `mem_fragmentation_ratio` indicates a more serious memory fragmentation issue.

Do not mistakenly think that the value of `used_memory_rss` minus `used_memory` is the size of memory fragmentation!!! This includes not only memory fragmentation but also overhead from other processes, as well as shared libraries, stacks, etc.

Many may ask, "What level of memory fragmentation ratio requires cleanup?"

Generally, we consider that a `mem_fragmentation_ratio > 1.5` indicates the need to clean up memory fragmentation. A `mem_fragmentation_ratio > 1.5` means that you need more than 3G of memory to store an actual size of 2G of data in Redis.

If you want to quickly check the memory fragmentation ratio, you can use the following command:

```bash
> redis-cli -p 637