I don't...
Title: Summary of Common Interviews in Redis (under)
Category: Database
Tag:

- Redis
  Head:
- Meta
- Name: keywords
  Content: Redis Foundation, Common Redis Data Structure, Redis Thread Model, Redis Memory Management, Redis Matters, Redis Performance Optimization
- Meta
- Name: description
  Content: An article summarizing the knowledge points and interview topics common to Redis, covering the Redis Foundation, the common Redis data structure, the Redis thread model, Redis memory management, Redis services, and Redis performance optimization.
  I don't...

@include: @article-header.snippet.md--

# Redis Affairs

# What's a Redis business?

You can interpret what is in Redis as: **Redis provides a function to package multiple command requests. All packing orders are then executed sequentially and will not be interrupted.**

The Redis service is used very little in its practical development, and its functions are relatively weak, so do not confuse it with the relational database that we normally understand.

In addition to not satisfying atomicity and durability, each order in the service interacts with the Redis server network, which is a waste of resources. It's only possible to do multiple orders in one batch. It's an operation you can't understand.

Therefore, Redis matters are not recommended for use in day-to-day development.

How do you use Redis affairs?

Redis may perform the services function by means of orders **`MULTI`, `EXEC`, `DISCARD`, and `WATCH`**.

```bash
> MULTI
Okay.
> SET PROJECT "JavaGuide"
QUEUED
> GET PROJECT
QUEUED
> EXEC
1) OK
"JavaGuide"
```

[`MULTI`](https://redis.io/commands/multi) commands can be followed by several orders, which Redis will not immediately execute, but rather place them in the queue and then execute all orders after [`EXEC`](https://redis.io/commands/exec) orders have been called.

Here's the process:

1. Commencement of business (`MULTI`);
1. Orders to enter the team (volume operation of Redis, first-in-first-out (FIFO) order);
1. Enforcement services (`EXEC`).

You can also order the cancellation of a matter through [`DISCARD`](https://redis.io/commands/discard), which will clear all the orders saved in the queue.

```bash
> MULTI
Okay.
> SET PROJECT "JavaGuide"
QUEUED
> GET PROJECT
QUEUED
> DISCARD
Okay.
```

You can listen to a designated Key by command [`WATCH`](https://redis.io/commands/watch), and when calling on the enforcement of the `EXEC` order, the whole matter will not be executed if a `WATCH` monitor is modified by **another client/session**.

```bash
# Client 1
> SET PROJECT "RustGuide"
Okay.
> WATCH PROJECT
Okay.
> MULTI
Okay.
> SET PROJECT "JavaGuide"
QUEUED

# Client 2
# Modify the value of PROJECT before client 1 executes EXEC commands for submission
> SET PROJECT "GoGuide"

# Client 1
# Changes failed because the value of PROJECT was modified by client 2
> EXEC
(nil)
> GET PROJECT
"GoGuide"
```

However, if **WATCH** and **business** are in the same session and the modified operation of Key, which is being monitored by **WATCH**, takes place inside the service, the matter can be successfully executed (relevant issue: [WATCH commands have different effects on MULTI commands](https://github.com/Snailclimb/JavaGuide/issues/1714)).

_Other Organizer_

```bash
> SET PROJECT "JavaGuide"
Okay.
> WATCH PROJECT
Okay.
> MULTI
Okay.
> SET PROJECT "JavaGuide1"
QUEUED
> SET PROJECT "JavaGuide2"
QUEUED
> SET PROJECT "JavaGuide3"
QUEUED
> EXEC
1) OK
2) OK
3) OK
127.0.0.1:6379 > GET PROJECT
"JavaGuide3"
```

_Other Organizer_

```bash
> SET PROJECT "JavaGuide"
Okay.
> WATCH PROJECT
Okay.
> SET PROJECT "JavaGuide2"
Okay.
> MULTI
Okay.
> GET USER
QUEUED
> EXEC
(nil)
```

(https://redis.io/topics/transactions)

(https://oss.javaguide.cn/github/javaguide/database/redis/redis-transactions.png)

# Does Redis support atomicity?

The Redis business is different from the relational database that we normally understand. We know that the matter has four main characteristics: **1. Atomicity**, **2. Separability**, **3. Persistence**, **4. Consistency**.

1. **Atomic**: Matters are the smallest implementing units and are not allowed to be divided. The atomic nature of the matter ensures that the action is either fully completed or completely ineffective;
1. \*\*
