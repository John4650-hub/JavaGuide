---
title: Summary of Common Blocking Reasons in Redis
category: Database
tag:
  - Redis
---

> This article is compiled and perfected from: <https://mp.weixin.qq.com/s/0Nqfq_eQrUb12QH6eBbHXA>, author: A Q Talks Code

This article will summarize in detail the situations that may cause Redis blocking, which are also key factors affecting Redis performance. Care should be taken when using Redis!

## O(n) Commands

Most commands in Redis have an O(1) time complexity, but there are also a few commands with O(n) time complexity, such as:

- `KEYS *`: Returns all keys that match the rules.
- `HGETALL`: Returns all key-value pairs in a Hash.
- `LRANGE`: Returns elements within a specified range in a List.
- `SMEMBERS`: Returns all elements in a Set.
- `SINTER`/`SUNION`/`SDIFF`: Calculates the intersection/union/difference of multiple Sets.
- ……

Due to the O(n) time complexity of these commands, sometimes a full table scan occurs. As n increases, the execution time will also increase, leading to client blocking. However, these commands are not entirely unusable, but the value of N needs to be clarified. If there is a need for traversal, `HSCAN`, `SSCAN`, or `ZSCAN` can be used instead.

In addition to these O(n) time complexity commands that may cause blocking, there are also commands with time complexities potentially greater than O(N), such as:

- `ZRANGE`/`ZREVRANGE`: Returns all elements within a specified ranking range in a Sorted Set. The time complexity is O(log(n)+m), where n is the total number of elements and m is the number of elements returned. When m and n are relatively large, the time complexity O(n) is smaller.
- `ZREMRANGEBYRANK`/`ZREMRANGEBYSCORE`: Removes all elements within a specified ranking range/specified score range in a Sorted Set. The time complexity is O(log(n)+m), where n is the total number of elements and m is the number of deleted elements. When m and n are relatively large, the time complexity O(n) is smaller.
- ……

## SAVE Creating RDB Snapshots

Redis provides two commands to generate RDB snapshot files:

- `save`: Synchronously saves operations and blocks the Redis main thread.
- `bgsave`: Forks a child process to execute, which does not block the Redis main thread, the default option.

By default, Redis will use the `bgsave` command. If you manually use the `save` command to generate RDB snapshot files, it will block the main thread.

## AOF

### AOF Log Record Blocking

Redis's AOF persistence mechanism records logs after executing commands, differing from relational databases (like MySQL), which usually log before executing commands for recovery purposes.

![AOF Log Recording Process](https://oss.javaguide.cn/github/javaguide/database/redis/redis-aof-write-log-disc.png)

**Why record logs after executing commands?**

- To avoid extra checking overhead, AOF log recording does not perform syntax checking on commands.
- Logging after command execution does not block the current command execution.

This also brings risks (which I mentioned when introducing AOF persistence):

- If Redis crashes right after executing a command, the corresponding modifications may be lost.
- **It may block the execution of subsequent commands (AOF log recording is conducted in the Redis main thread).**

### AOF Flush Blocking

After enabling AOF persistence, every command that changes data in Redis will write that command to the AOF buffer `server.aof_buf`, and then according to the `appendfsync` configuration, it decides when to synchronize it to the AOF file on the hard disk.

There are three different AOF persistence methods (fsync strategy) in Redis's configuration file:

1. `appendfsync always`: The main thread calls `write` to perform a write operation, after which the background thread (`aof_fsync` thread) immediately calls the `fsync` function to sync the AOF file (flush). After `fsync` completes, the thread returns; this will severely decrease Redis's performance (write + fsync).
1. `appendfsync everysec`: The main thread calls `write` to perform a write operation and immediately returns, with the background thread (`aof_fsync` thread) calling the `fsync` function once every second to sync the AOF file (write + fsync, the fsync interval is 1 second).
1. `appendfsync no`: The main thread calls `write` to perform a write operation and immediately returns, allowing the operating system to decide when to sync, which is usually once every 30 seconds on Linux (write but no fsync; the timing of fsync is determined by the operating system).

When the background thread (`aof_fsync` thread) calls the `fsync` function to sync the AOF file, it needs to wait until the writing is complete. When there is too much disk pressure, blocking may occur during the `fsync` operation, and the main thread calling `write` will also be blocked. The main thread can only return successfully after `fsync` completes.

For a detailed introduction to the AOF workflow, you can refer to: [Detailed Explanation of Redis Persistence Mechanism](./redis-persistence.md), which helps understand AOF flush blocking.

### AOF Rewrite Blocking

1. A child thread is forked to rewrite the file. When executing the `BGREWRITEAOF` command, the Redis server maintains an AOF rewrite buffer that records all write commands executed by the server during the child thread's creation of the new AOF file.
1. After the child thread completes the task of creating the new AOF file, the server appends all content from the rewrite buffer to the end of the new AOF file, ensuring that the new AOF file preserves the database state consistent with the existing database state.
1. Finally, the server replaces the old AOF file with the new AOF file to complete the AOF file rewrite operation.

Blocking occurs during the process of step 2 when writing new data from the buffer to the new file, causing a **block**.

For further reading: [Analysis of Redis AOF Rewrite Blocking Issues](https://cloud.tencent.com/developer/article/1633077).

## Big Key

If the value corresponding to a key occupies a large amount of memory, that key can be considered a big key. What exactly counts as large? There is a rather imprecise reference standard:

- String-type values exceeding 1MB.
- Composite types (lists, hashes, sets, sorted sets, etc.) whose values contain more than 5000 elements (for composite types, having more elements does not necessarily mean occupying more memory).

The blocking issues caused by big keys are as follows:

- Client timeout blocking: Since Redis executes commands in a single-threaded manner, operations on big keys can be time-consuming, causing Redis to block, resulting in the client not responding for an extended period.
- Causing network blocking: The network traffic generated when retrieving big keys is relatively large. If a key's size is 1MB and the access frequency is 1000 per second, it will generate 1000MB of traffic per second, which is disastrous for a typical gigabit network card server.
- Blocking worker threads: If deleting a big key using del, it will block worker threads, preventing subsequent commands from being processed.

### Finding Big Keys

When using Redis's built-in `--bigkeys` parameter to find big keys, it is best to execute the command on a slave node, as executing it on the master node will **block** the master node.

- We can also use the SCAN command to find big keys;
- Analyzing RDB files to identify big keys requires that Redis is using RDB persistence. There are tools available online:
  - redis-rdb-tools: A Python tool for analyzing Redis RDB snapshot files.
  - rdb_bigkeys: A Go tool for analyzing Redis RDB snapshot files, which performs better.

### Deleting Big Keys

The essence of the delete operation is to free the memory space occupied by key-value pairs.

Freeing memory is just the first step. To manage memory space more efficiently, when an application releases memory, **the operating system needs to insert the released memory blocks into a linked list of free memory blocks** for subsequent management and reallocation. This process itself requires some time and can **block** the current application that is freeing memory.

Therefore, if a large amount of memory is released at once, the time required for managing the linked list of free memory blocks will increase, consequently causing blocking in the Redis main thread. If the main thread becomes blocked, all other requests may timeout, leading to increasing timeouts that can exhaust Redis connections and generate various exceptions.

When deleting big keys, it is recommended to use batch deletions and asynchronous deletions.

## Clearing the Database

Clearing the database is similar to the deletion of big keys; `flushdb` and `flushall` involve the deletion and release of all key-value pairs, which are also blocking points in Redis.

## Cluster Expansion

Redis clusters can dynamically scale up and down nodes. This process is currently semi-automated and requires manual intervention.

During the expansion or contraction, data migration is needed. Redis ensures the consistency of migration; all migration operations are synchronous.

During migration, both ends of Redis will enter a blocking state for varying durations. For small keys, this time can be negligible, but if a key's memory usage is too large, it can trigger failover within the cluster, causing unnecessary switching.

## Swap

**What is Swap?** Swap refers to the exchange, and in Linux, Swap is often called memory swapping or swap partition. Similar to virtual memory in Windows, it solves memory shortages by virtualizing some hard disk space to be used as memory. Thus, the purpose of the Swap partition is to sacrifice hard disk space to increase memory and address the issue of insufficient or overloaded VPS memory.

Swap can be very detrimental to Redis. One important premise for Redis to ensure high performance is that all data exists in memory. If the operating system swaps out some of the memory used by Redis to the hard disk, the read and write speeds of memory and hard disk differ by several orders of magnitude, leading to a drastic decline in Redis performance after the swap.

Here are methods to check if Redis is experiencing Swap:

1. Query the Redis process ID

```bash
redis-cli -p 6383 info server | grep process_id
process_id: 4476
```

2. Check memory swap information based on the process ID

```bash
cat /proc/4476/smaps | grep Swap
Swap: 0kB
Swap: 0kB
Swap: 4kB
Swap: 0kB
Swap: 0kB
.....
```

If the swap amounts are all 0KB or a few are 4KB, it is normal.

Methods to prevent memory swapping:

- Ensure ample available memory on the machine.
- Ensure all Redis instances set a maximum available memory (maxmemory) to prevent uncontrolled growth of Redis memory in extreme situations.
- Lower the system's swap priority, such as `echo 10 > /proc/sys/vm/swappiness`.

## CPU Competition

Redis is a typical CPU-intensive application and should not be deployed alongside other multi-core CPU-intensive services. When other processes excessively consume CPU, it will severely affect Redis's throughput.

You can use `redis-cli --stat` to get the current Redis usage. The `top` command can be used to get information on processes' CPU utilization, and `info commandstats` can provide statistics to analyze unreasonable command overhead, checking if it is due to high algorithm complexity or excessive memory optimization issues.

## Network Issues

Connection refusals, network delays, software interrupts on network cards, and other network issues might also lead to Redis blocking.

## References

- Analysis and Summary of 6 Major Scenarios of Redis Blocking: <https://mp.weixin.qq.com/s/eaZCEtTjTuEmXfUubVHjew>
- Redis Development and Operation Notes - The Nightmare of Redis - Blocking: <https://mp.weixin.qq.com/s/TDbpz9oLH6ifVv6ewqgSgA>

<!-- @include: @article-footer.snippet.md -->
