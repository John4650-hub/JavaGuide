I don't...
title: Summary of common realization options for distributed locks
Category: Distribution
I don't...

@include: @small-advertition.snippet.md-->

Normally, we usually choose distributed locks based on Redis or ZooKeeper, and Redis uses them a little more, and I'll start with Redis, for example.

# Based on Redis achieves a distributed lock

# How to achieve the simplest distributed lock based on Redis?

Whether it is a local or a distributed lock, the core is “crust”.

In Redis, the `SETNX` order can help us achieve mutual exclusion. `SETNX` is **SET** if **N**ot e**X**ists (relative to the `setIfAbsent` method in Java) to set the value of the key if the key does not exist. If the key already exists, `SETNX` does nothing.

```bash
> SETNX LockKey uniqueValue
(integer) 1
> SETNX LockKey uniqueValue
(integer) 0
```

If the lock is released, the corresponding key can be deleted directly by the `DEL` command.

```bash
> DEL LockKey
(integer) 1
```

In order to prevent the wrong deletion of other locks, we suggest using a Lua script to judge by the value of the key.

Lua scripts are selected to ensure the atomicity of the unlock operation. Because, while implementing the Lua script, Redis can do it in an atomic manner, thus ensuring the atomicity of the lock release operation.

```lua
-- When the lock is released, compare the value of the lock with the value and avoid the wrong release of the lock
if redis.call("get", KEYS[1]) == ARGV[1] then
    return redis.call("del", KEYS[1])
else
    return 0
end
```

[Redis achieves a simple distributed lock](https://oss.javaguide.cn/github/javaguide/distributed-system/distributed-lock/distributed-lock-setnx.png)

This is the easiest Redis distributed lock to achieve in a simpler and more efficient way. However, there are some problems with this way of achieving a distributed lock. For example, applications encounter problems such as the sudden suspension of the logic of unlocking the lock, which may result in the lock not being released and, consequently, the sharing of resources not being accessed by other threads/processes.

Why set an expiration time for the lock?

One of the solutions we can think of in order to avoid the lock not being released is: **Set an expiry date for this key (i.e., the lock).**

```bash
127.0.0.1:6379> SET lockKey uniqueValue EX 3 NX
OK
```

- **lockKey**: Locked lock name;
- **uniqueValue**: random strings capable of uniquely identifying locks;
- **NX**: only when the key value of LockKey does not exist can SET succeed;
- **EX**: Expiration Time Settings (in seconds) EX 3 indicates that this lock has an automatic expiry time of 3 seconds. The EX corresponds to PX (in milliseconds), both of which are outdated time settings.

**Make sure that setting the specified key value and expiry time is an atomic operation!** Otherwise, there may still be problems of locks not being released.

This does solve problems, but there are also gaps in this solution: **If the time of operating the shared resources is longer than the expiry time, there will be problems with the early expiration of the lock, which will lead to the direct breakdown of the distributed lock. If the lock is too long, it affects performance.**

You may wonder: **If the operation of shared resources is not complete, the lock can be renewed!**

# How do you achieve the elegance of the lock?

For the small partners developed by Java, solutions are available: **[Redisson](https://github.com/redisson/redisson)**. Solutions for other languages can be found in the Redis official document at: <https://redis.io/topics/distlock>.

[Distributed locks with Redis]

Redisson is an open-source Java language Redis client that provides many open-boxing functions, not just the realization of multiple distributed locks. Moreover, Redisson supports a variety of deployment structures such as Redis single, Redis Sentinel, and Redis Cluster.

The distributed lock in Redisson, with its own automatic renewal mechanism, is very simple to use, and its rationale is relatively simple. It provides a special monitor and renewal lock **watchdog**, which will continue to extend the time of the lock to ensure that it is not released due to time lapses if the thread operating the shared resource has not been completed.

![Redisson Watchdog](https://oss.javaguide.cn/github/javaguide/distributed-system/distributed-lock/distributed-lock-redisson-renew-expiration.png)

The name of the watchdog is derived from the `getLockWatchdogTimeout()` method, which returns the expiry time of the locking period and
