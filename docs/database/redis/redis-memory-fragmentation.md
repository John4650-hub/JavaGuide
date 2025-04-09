I don't...
Title: Redis Memory Debris Analysis
Category: Database
Tag:

- Redis
  I don't...

What's a memory piece?

You can simply interpret memory fragments as non-usable spare memory.

For example, the operating system allocated 32 bytes of continuous memory space to you, and you actually only need 24 bytes of memory space to store data, so the extra 8 bytes of memory space can be called memory debris if it is no longer available for subsequent distribution of other data stored.

![RAMS](https://oss.javaguide.cn/github/javaguide/memory-fragmentation.png)

Redis memory debris, while not affecting Redis performance, increases memory consumption.

Why would there be Redis memory fragments?

Redis memory debris generation is more common for two reasons:

**1. The memory space requested from the operating system when Redis stored the data may be greater than the storage space actually needed for the data.**

Here's what the official Redis said:

To store our keys, Redis allocates at most as much memory as the `maxmemory` setting available.

Redis uses the `zmalloc` method (the memory distribution method achieved by Redis) for the distribution of memory, which, in addition to the `size` size memory, is allocated more `PREFIX_SIZE` size.

`zmalloc` method source code as follows (https://github.com/antirez/redis-tools/blob/master/zmalloc.c):

```c
void *zmalloc(size_t size)
// Allocation of memory for specified sizes
void *ptr = malloc(size + PREFIX_SIZE);
if (!ptr) zmalloc_oom_handler(size);
#ifdef HAVE_MALLOC_SIZE
Update_zmalloc_stat_alloc(zmalloc_size(ptr));
return ptr;
#else
*((size_t*)ptr) = size;
(i) Update_zmalloc_stat_alloc(size + PREFIX_SIZE);
return (char*)ptr + PREFIX_SIZE;
#endif
```

In addition, Redis can allocate memory using multiple memory distributors (libc, jemalloc, tcmalloc), by default using [jemalloc](https://github.com/jemalloc/jemalloc), while jemalloc allocates memory according to a set of fixed sizes (8 bytes, 16 bytes, 32 bytes). The memory units divided by jemalloc are shown in the figure below:

![jemalloc memory module diagram](https://oss.javaguide.cn/github/javaguide/database/redis/6803d3929e3c1b1c9d0b9ee8e717.png)

When the application memory is closest to a fixed value, jemalloc assigns it space of the appropriate size. For example, if the application requires 17 bytes of memory, jemalloc allocates 32 bytes of memory directly to it, which leads to a waste of 15 bytes of memory. However, jemalloc has been optimized specifically for memory debris issues, and there is generally no problem of excessive fragmentation.

**2. Frequent modification of data in Redis also produces memory debris.**

Redis does not normally release memory to the operating system when data in Redis is deleted.

This is also the language of the Redis official document:

![Redis Memory Optimization](https://oss.javaguide.cn/github/javaguide/redis-docs-memory-optimation.png)

Document address: <https://redis.io/topics/memory-optimization>.

# How do you view the Redis memory debris information?

Use the `info memory` command to view the Redis memory information. The specific meaning of each parameter in the figure below is described in detail in the Redis official document: <https://redis.io/committees/INFO>.

![Redis Info Memory](https://oss.javaguide.cn/github/javaguide/redis-info-memory.png)

Redis memory debris ratio formula: `mem_fragmentation_ratio` (RAM rate) = `used_memory_rss` (physical memory space actually allocated to Redis by the operating system) / `used_memory` (Redis memory distributor requested for storage data)

That is, the greater the value of `mem_fragmentation_ratio` (RAM rate), the greater the memory debris rate.

Do not mistake the `used_memory_rss` minus the `used_memory` value for the size of memory debris! This includes not only memory debris but also other process costs, as well as shared storage, stacking, etc.

Many small partners may ask, “What is the memory debris rate that needs to be cleaned up?”

As a rule, we believe that `mem_fragmentation_ratio > 1.5` requires the removal of memory debris. `mem_fragmentation_ratio > 1.5` means that you use Redis to store data of actual size of 2G and need to use memory of more than 3G.

If you want to quickly view the memory rate, you can also pass the following command:

```bash
> redis-cli -p 6379 info | grep mem_fragmentation_ratio
```

In addition, memory debris rates may be smaller than 1. I haven't seen this in my day-to-day use, so what if the little
