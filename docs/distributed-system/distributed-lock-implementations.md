---
title: Summary of Common Distributed Lock Implementation Solutions
category: Distributed
---

<!-- @include: @small-advertisement.snippet.md -->

In general, we usually choose to implement distributed locks based on Redis or ZooKeeper, with Redis being the more commonly used option. Here, I will introduce the implementation of distributed locks using Redis as an example.

## Implementing Distributed Locks Based on Redis

### How to Implement a Simple Distributed Lock Using Redis?

Whether it's a local lock or a distributed lock, the core concept is "mutual exclusion."

In Redis, the `SETNX` command helps us achieve mutual exclusion. `SETNX` stands for **SET** if **N**ot e**X**ists (corresponding to the `setIfAbsent` method in Java). It sets the key's value only if the key does not exist. If the key already exists, `SETNX` does nothing.

```bash
> SETNX lockKey uniqueValue
(integer) 1
> SETNX lockKey uniqueValue
(integer) 0
```

To release the lock, simply delete the corresponding key using the `DEL` command.

```bash
> DEL lockKey
(integer) 1
```

To prevent accidentally deleting other locks, it is recommended to use a Lua script to check the value (unique value) corresponding to the key.

Using a Lua script ensures the atomicity of the unlock operation. When Redis executes a Lua script, it does so atomically, thus guaranteeing the atomicity of the lock release operation.

```lua
// When releasing the lock, first compare the value of the lock to avoid accidental release
if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
```

![Simple Distributed Lock Implementation with Redis](https://oss.javaguide.cn/github/javaguide/distributed-system/distributed-lock/distributed-lock-setnx.png)

This is a simple implementation of a Redis distributed lock, which is straightforward and efficient. However, this method has some issues. For example, if the application encounters a problem and the logic for releasing the lock suddenly crashes, it may lead to the lock not being released, causing shared resources to be inaccessible to other threads/processes.

### Why Set an Expiration Time for the Lock?

To avoid the lock being unreleased, one solution is to **set an expiration time for this key (the lock)**.

```bash
127.0.0.1:6379> SET lockKey uniqueValue EX 3 NX
OK
```

- **lockKey**: The name of the lock;
- **uniqueValue**: A random string that uniquely identifies the lock;
- **NX**: The SET operation will only succeed if the key corresponding to lockKey does not exist;
- **EX**: Sets the expiration time (in seconds). EX 3 indicates that this lock has an automatic expiration time of 3 seconds. The counterpart to EX is PX (in milliseconds), and both are used for setting expiration times.

**It is crucial to ensure that setting the specified key's value and expiration time is an atomic operation!!!** Otherwise, the issue of the lock not being released may still occur.

This indeed solves the problem, but this solution also has vulnerabilities: **If the time taken to operate on the shared resource exceeds the expiration time, the lock may expire prematurely, leading to the distributed lock becoming invalid. If the timeout is set too long, it may affect performance.**

You might be wondering: **If the operation on the shared resource is not yet complete, it would be great if the lock's expiration time could be renewed automatically!**

### How to Implement Elegant Lock Renewal?

For Java developers, there is already a ready-made solution: **[Redisson](https://github.com/redisson/redisson)**. Solutions for other languages can be found in the official Redis documentation at: <https://redis.io/topics/distlock>.

![Distributed locks with Redis](https://oss.javaguide.cn/github/javaguide/redis-distributed-lock.png)

Redisson is an open-source Redis client for Java that provides many out-of-the-box features, including various implementations of distributed locks. Additionally, Redisson supports multiple deployment architectures such as Redis standalone, Redis Sentinel, and Redis Cluster.

The distributed lock in Redisson comes with an automatic renewal mechanism, making it very easy to use. The principle is quite simple: it provides a dedicated **Watch Dog** to monitor and renew the lock. If the thread operating on the shared resource has not completed its execution, the Watch Dog will continuously extend the lock's expiration time, ensuring that the lock is not released due to timeout.

![Redisson Watch Dog Automatic Renewal](https://oss.javaguide.cn/github/javaguide/distributed-system/distributed-lock/distributed-lock-redisson-renew-expiration.png)

The name "Watch Dog" comes from the \`getLockWatchdog
