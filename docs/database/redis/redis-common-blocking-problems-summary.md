I don't...
Title: Summary of common obstructions in Redis
Category: Database
Tag:

- Redis
  I don't...

> This document has been refined from: <https://mp.weixin.qq.com/s/0Nqfq_eQrUb12QH6eBbHXA> by: A Q speaking code

This article will provide a detailed summary of the conditions that could lead to Redis obstruction, which is also a key factor affecting Redis performance, and should be of particular interest when using Redis!

# O(n) command

Most of the Redis commands are O(1) time complexity, but there are also a few O(n) time complexity commands, such as:

- `KEYS *`: returns all rule-compliant keys.
- `HGETALL`: returns all key-value pairs in a Hash.
- `LRANGE`: returns the elements within the range specified in a List.
- `SMEMBERS`: returns all elements in a Set.
- `SINTER`, `SUNION`, `SDIFF`: calculates the intersection/sum/differences of multiple Sets.
- ...

As the complexity of these commands is O(n), they can sometimes be fully scanned, and as n increases, the longer the execution time, which leads to closure by the client. However, these commands are not necessarily unserviceable, but need to be specified as N. In addition, `HSCAN`, `SSCAN`, `ZSCAN` could be replaced with a `HSCAN`.

In addition to these O(n) time complexity commands, which may lead to congestion, there are also commands whose time complexity may be greater than O(N), such as:

- `ZRANGE`, `ZREVRANGE`: returns all the elements within the assigned ranking in a Sorted Set. Time complexity is O(log(n)+m), where n is the number of all elements, m is the number of elements returned, and O(n) time complexity is smaller when m and n are fairly large.
- `ZREMRANGEBYRANK`, `ZREMRANGEBYSCORE`: removes all the elements in the Sorted Set specified by range/score. Time complexity is O(log(n)+m), where n is the number of all elements, m is the number of deleted elements, and O(n) time complexity is smaller when m and n are fairly large.
- ...

# SAVE Create RDB Snapshot

Redis provides two commands to generate RDB snapshots:

- `SAVE`: Synchronous save operations that block the Redis main thread;
- `BGSAVE`: forks a subprocess, which is executed without blocking the Redis main thread, default option.

By default, the Redis configuration will use the `BGSAVE` command. The main thread is blocked if the `SAVE` command is used manually to generate an RDB snapshot.

# AOF

## AOF log block

The Redis AOF persistence mechanism records logs after the command has been executed, which is different from relational databases (e.g., MySQL) that usually record logs (facilitating failure recovery) before the command is executed.

![AOF Log Process](https://oss.javaguide.cn/github/javaguide/database/redis/redis-aof-write-log-disc.png)

**Why record the log after the execution of the command?**

- Avoiding additional checks of expenses, and the AOF logs do not perform grammatical checks of commands;
- Recording after the command has been executed will not block the current command.

This brings with it risks (as I mentioned earlier when I introduced AOF persistence):

- If the command is executed, Redis has the opportunity to lose the corresponding modification;
- **May block the execution of other subsequent commands (AOF logs are carried out in the Redis main thread)**.

# AOF Brush Block

When AOF persists for every command that changes the data in Redis, Redis writes the command into the AOF buffer `server.aof_buf` and then determines when it will be synchronized to the AOF file on the hard drive according to the `appendfsync` configuration.

There are three different AOF persistence methods (`fsync` strategy) in the Redis configuration file:

1. `appendfsync always`: The `write` execution of the `write` operation will be immediately followed by the `fsync` function to synchronize the AOF file (paint) and `fsync` completion of the back-line return, which will seriously reduce the performance of Redis (`write` + `fsync`).
1. `appendfsync everysec`: the main thread calls `write` and returns immediately after performing writing, and calls the `fsync` function (system call) every second from the back channel (`aof_fsync`) to synchronize the AOF file (`write` + `fsync`, `fsync` at 1-second intervals).
1. `appendfsync no`: the main thread returns immediately after the `write` execution writing operation, allowing the operating system to determine when to synchronize, and Linux is normally 30 seconds below (`write`
