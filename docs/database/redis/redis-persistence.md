I don't...
Title: Redis Sustainability Mechanism\
Category: Database\
Tag:

- Redis\
  Head:
- Meta
- Name: keywords\
  Content: Redis Sustainability Mechanism
- Meta
- Name: description\
  The important thing about Redis, unlike Memcached, is that Redis supports durability and supports three ways of durability: snapshots, RDBs, and append-only files (AOF).

When using the cache, we often need to maintain the data in memory, i.e., to write the data in memory into the hard drive. This is due in large part to subsequent re-use of data (e.g., restarting machines, recovery of data after machine failure) or to data synchronization (e.g., Redis cluster master node synchronizing data through RDB files).

The important thing about Redis, unlike Memcached, is that Redis supports permanence and supports three permanence approaches:

- Snapshot
- Append-only file (AOF)
- Combination of RDB and AOF (new in Redis 4.0)

Official document address: <https://redis.io/topics/business>.

![Redis Persistence](https://oss.javaguide.cn/github/javaguide/database/redis/redis4.0-persitence.png)

# RDB Endurance

## What's RDB Endurance?

Redis can get a copy of the stored data at **some time** by creating a snapshot. Redis creates snapshots that can be backed up, which can be copied to another server to create a copy of a server with the same data (Redis master structure, mainly to enhance Redis performance), and can be left in place to be used when the server is restarted.

RDB endurance is the default durability approach for Redis, which is configured by default in the `redis.conf` configuration file:

```clojure
Save 900 1 # after 900 seconds (15 minutes) if at least one key changes, Redis automatically triggers bgsave to create a snapshot.  

Save 300 10 # after 300 seconds (5 minutes) if at least 10 keys change, Redis automatically triggers bgsave command to create a snapshot.  

Save 60 10000 # after 60 seconds (1 minute) if at least 10,000 keys change, Redis automatically triggers bgsave command to create a snapshot.  
```

Does RDB block the main thread when creating a snapshot?

Redis provides two commands to generate RDB snapshots:

- `save`: Synchronous save operation that blocks the Redis main thread;
- `bgsave`: Forks a subprocess, which is executed without blocking the Redis main thread, the default option.

> This means that the main process of Redis is primarily responsible for the main work done after Redis' launch, mainly by a single thread. If you want to describe it as the Redis main process, that's fine.

# AOF Endurance

## What's AOF Endurance?

AOF is much better in real-time than RDB. By default, Redis does not enable the durability of the AOF (append-only file) method (since Redis 6.0, it has been enabled by default), which can be turned on by the `appendonly` parameter:

```bash
# It's not like you're going to do it.  
```

With each AOF command that changes the data in Redis, Redis writes the command into the AOF buffer `server.aof_buf` and then into the AOF file (at which time the system's memory cache is not synchronized to disk) and then decides when to synchronize the data from the system's memory cache to the hard disk, depending on the configuration of the durability approach (`fsync` policy).

Only synchronizing to disk is sustainable, or there is a risk that data will be lost. For example, if data from the system's memory caches are not synchronized and the disk machine is detached, then this part of the data will be lost.

The AOF file is stored at the same location as the RDB file, set by the `dir` parameter, and the default file name is `appendonly.aof`.

## AOF, what's the basic process?

AOF sustainability can be achieved in a simple 5-step process:

1. **Append**: All written commands will be added to the AOF buffer.
1. **Document written (write)**: AOF buffer data is written into AOF files. This step requires a call to the `write` function (system call), and `write` returns directly after writing the data into the system's memory buffer (delayed writing). Attention! The disk is not synchronized at this time.
1. **Document Synchronization (fsync)**: AOF buffer synchronizes with hard disks according to the corresponding durability (`fsync` strategy). This step requires a call to the `fsync` function (system call). `fsync` is for single file operations, which forces the synchronization of
