---
title: Summary of Common Redis Interview Questions (Part 2)
category: Database
tag:
  - Redis
head:
  -   - meta
      - name: keywords
        content: Redis Basics, Common Redis Data Structures, Redis Thread Model, Redis Memory Management, Redis Transactions, Redis Performance Optimization
  -   - meta
      - name: description
        content: An article summarizing common knowledge points and interview questions about Redis, covering Redis basics, common data structures, thread model, memory management, transactions, and performance optimization.
---

<!-- @include: @article-header.snippet.md -->

## Redis Transactions

### What is a Redis Transaction?

You can understand a transaction in Redis as: **Redis transactions provide a way to package multiple command requests together. Then, all the packaged commands are executed in order without being interrupted.**

In actual development, Redis transactions are used very rarely, and their functionality is somewhat limited. Do not confuse them with transactions in relational databases as we usually understand them.

In addition to not satisfying atomicity and durability, each command in a transaction interacts with the Redis server over the network, which is a resource-wasting behavior. Clearly, multiple commands can be executed in a batch, so this operation is hard to understand.

Therefore, Redis transactions are not recommended for use in daily development.

### How to Use Redis Transactions?

Redis can implement transaction functionality through commands like **`MULTI`, `EXEC`, `DISCARD`, and `WATCH`**.

```bash
> MULTI
OK
> SET PROJECT "JavaGuide"
QUEUED
> GET PROJECT
QUEUED
> EXEC
1) OK
2) "JavaGuide"
```

After the [`MULTI`](https://redis.io/commands/multi) command, you can input multiple commands. Redis will not execute these commands immediately but will queue them. When the [`EXEC`](https://redis.io/commands/exec) command is called, all commands are executed.

The process is as follows:

1. Start the transaction (`MULTI`);
1. Queue commands (commands for batch operations on Redis are executed in a first-in-first-out (FIFO) order);
1. Execute the transaction (`EXEC`).

You can also cancel a transaction using the [`DISCARD`](https://redis.io/commands/discard) command, which will clear all commands saved in the transaction queue.

```bash
> MULTI
OK
> SET PROJECT "JavaGuide"
QUEUED
> GET PROJECT
QUEUED
> DISCARD
OK
```

You can use the [`WATCH`](https://redis.io/commands/watch) command to monitor a specified key. When the `EXEC` command is called to execute the transaction, if a key being monitored by the `WATCH` command is modified by **another client/session**, the entire transaction will not be executed.

```bash
# Client 1
> SET PROJECT "RustGuide"
OK
> WATCH PROJECT
OK
> MULTI
OK
> SET PROJECT "JavaGuide"
QUEUED

# Client 2
# Modifies the value of PROJECT before Client 1 executes the EXEC command to submit the transaction
> SET PROJECT "GoGuide"

# Client 1
# Modification fails because the value of PROJECT was changed by Client 2
> EXEC
(nil)
> GET PROJECT
"GoGuide"
```

However, if **WATCH** and **transaction** are in the same session, and the operation that modifies the monitored key occurs within the transaction, the transaction can be successfully executed (related issue: [Different Effects of WATCH Command When Encountering MULTI Command](https://github.com/Snailclimb/JavaGuide/issues/1714)).

Modifying the WATCH monitored key inside the transaction:

```bash
> SET PROJECT "JavaGuide"
OK
> WATCH PROJECT
OK
> MULTI
OK
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
127.0.0.1:6379> GET PROJECT
"JavaGuide3"
```

Modifying the WATCH monitored key outside the transaction:

```bash
> SET PROJECT "JavaGuide"
OK
> WATCH PROJECT
OK
> SET PROJECT "JavaGuide2"
OK
> MULTI
OK
> GET USER
QUEUED
> EXEC
(nil)
```

The official Redis website provides related information [https://redis.io/topics/transactions](https://redis.io/topics/transactions) as follows:

![Redis Transactions](https://oss.javaguide.cn/github/javaguide/database/redis/redis-transactions.png)

### Does Redis Transactions Support Atomicity?

Redis transactions are different from what we usually understand as transactions in relational databases. We know that transactions have four major characteristics: **1. Atomicity**, **2. Isolation**, **3. Durability**,
