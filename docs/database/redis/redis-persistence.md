---
title: Detailed Explanation of Redis Persistence Mechanisms
category: Database
tag:
  - Redis
head:
  - - meta
    - name: keywords
      content: Detailed Explanation of Redis Persistence Mechanisms
  - - meta
    - name: description
      content: One important difference between Redis and Memcached is that Redis supports persistence, and it supports three types of persistence methods: snapshots (snapshotting, RDB), append-only files (append-only file, AOF), and hybrid persistence of RDB and AOF (new in Redis 4.0).
---

When using caching, we often need to persist data in memory, which means writing the data in memory to disk. The main reasons are to reuse data later (for example, to recover data after restarting the machine or after a machine failure) or to synchronize data (for example, Redis cluster master-slave nodes synchronize data through RDB files).

One important difference between Redis and Memcached is that Redis supports persistence, and it supports three types of persistence methods:

- Snapshots (snapshotting, RDB)
- Append-only files (append-only file, AOF)
- Hybrid persistence of RDB and AOF (new in Redis 4.0)

Official documentation link: <https://redis.io/topics/persistence>.

![](https://oss.javaguide.cn/github/javaguide/database/redis/redis4.0-persitence.png)

## RDB Persistence

### What is RDB Persistence?

Redis can create snapshots to obtain a copy of the data stored in memory at **a certain point in time**. After Redis creates a snapshot, it can back it up, copy the snapshot to other servers to create replicas with the same data (the Redis master-slave structure, mainly used to improve Redis performance), or keep the snapshot in place for use when restarting the server.

Snapshot persistence is the default persistence method used by Redis, and the following configuration is present by default in the `redis.conf` configuration file:

```clojure
save 900 1           # After 900 seconds (15 minutes), if at least 1 key has changed, Redis will automatically trigger the bgsave command to create a snapshot.

save 300 10          # After 300 seconds (5 minutes), if at least 10 keys have changed, Redis will automatically trigger the bgsave command to create a snapshot.

save 60 10000        # After 60 seconds (1 minute), if at least 10000 keys have changed, Redis will automatically trigger the bgsave command to create a snapshot.
```

### Does RDB Block the Main Thread When Creating Snapshots?

Redis provides two commands to generate RDB snapshot files:

- `save`: A synchronous save operation that blocks the Redis main thread;
- `bgsave`: Forks a child process to execute, which does not block the Redis main thread, the default option.

> Here, we refer to the Redis main thread rather than the main process mainly because Redis primarily completes its main work in a single-threaded manner after startup. If you want to describe it as the Redis main process, that is also acceptable.

## AOF Persistence

### What is AOF Persistence?

Compared to snapshot persistence, AOF persistence has better real-time performance. By default, Redis does not enable AOF (append-only file) persistence (it has been enabled by default since Redis 6.0), and it can be enabled through the `appendonly` parameter:

```bash
appendonly yes
```

After enabling AOF persistence, every command that modifies data in Redis will write that command to the AOF buffer `server.aof_buf`, and then write it to the AOF file (at this point, it is still in the system kernel cache and has not been synchronized to disk). Finally, it decides when to synchronize the data in the system kernel cache to the hard disk based on the persistence method (`fsync` strategy) configuration.

Only when synchronized to disk is it considered persistently saved; otherwise, there is still a risk of data loss. For example, if the data in the system kernel cache has not been synchronized and the disk machine crashes, that portion of data will be lost.

The AOF file's save location is the same as that of the RDB file, both set by the `dir` parameter, and the default filename is `appendonly.aof`.

### What is the Basic Workflow of AOF?

The implementation of AOF persistence can be simply divided into 5 steps:

1. **Command Append (append)**: All write commands are appended to the AOF buffer.
1. **File Write (write)**: The data in the AOF buffer is written to the AOF file. This step requires calling the `write` function (system call), and `write` returns directly after writing the data to the system kernel buffer (delayed write). Note!!! At this point, it has not been synchronized to disk.
1. \*\*File Synchron
