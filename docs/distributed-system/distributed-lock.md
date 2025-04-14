---
title: Introduction to Distributed Locks
category: Distributed
---

<!-- @include: @small-advertisement.snippet.md -->

There are many articles online about distributed locks, and I have written a relatively concise and easy-to-understand version that should be sufficient for interviews and work.

In this article, we will first introduce the basic concepts of distributed locks.

## Why Do We Need Distributed Locks?

In a multithreaded environment, if multiple threads access shared resources (such as product inventory or food delivery orders) simultaneously, data contention can occur, potentially leading to dirty data or system issues, threatening the normal operation of the program.

For example, suppose there are 100 users participating in a limited-time flash sale, with each user allowed to purchase 1 item, and there are only 3 items available. If mutual exclusion is not applied to the shared resource, the following situation may occur:

- Threads 1, 2, 3, etc., enter the purchase method simultaneously, with each thread corresponding to a user.
- Thread 1 queries the number of items the user has already purchased, finds that the current user has not yet purchased and that there is still 1 item in stock, and thus believes it can continue the purchase process.
- Thread 2 also executes the query for the number of items the user has already purchased, finds that the current user has not yet purchased and that there is still 1 item in stock, and thus believes it can continue the purchase process.
- Thread 1 continues to execute, reducing the stock quantity by 1, and then returns success.
- Thread 2 continues to execute, reducing the stock quantity by 1, and then returns success.
- At this point, an overselling issue occurs, resulting in the product being sold more than once.

![Issues caused by non-mutual access to shared resources](https://oss.javaguide.cn/github/javaguide/distributed-system/distributed-lock/oversold-without-locking.png)

To ensure that shared resources are accessed safely, we need to use mutual exclusion operations to protect shared resources, allowing only one thread to access the shared resource at any given time, while other threads must wait until the current thread releases it. This can avoid data contention and dirty data issues, ensuring the correctness and stability of the program.

**How can we achieve mutual access to shared resources?** Locks are a relatively general solution, more specifically, pessimistic locks.

Pessimistic locks always assume the worst-case scenario, believing that issues will arise every time shared resources are accessed (for example, shared data being modified). Therefore, every time a resource operation is performed, a lock is acquired, causing other threads that want to access this resource to block until the lock is released by the previous holder. In other words, **the shared resource is used by only one thread at a time, while other threads are blocked, and after use, the resource is transferred to other threads**.

For single-machine multithreading, in Java, we typically use the `ReentrantLock` class or the `synchronized` keyword, which are built-in **local locks** provided by the JDK, to control access to local shared resources by multiple threads within a single JVM process.

Here is a diagram I created to illustrate local locks.

![Local Lock](https://oss.javaguide.cn/github/javaguide/distributed-system/distributed-lock/jvm-local-lock.png)

From the diagram, it can be seen that these threads access shared resources mutually exclusively, with only one thread able to acquire the local lock to access the shared resource at any given time.

In a distributed system, different services/clients typically run in separate JVM processes. If multiple JVM processes share the same resource, local locks cannot achieve mutual access to the resource. Thus, **distributed locks** were born.

For example: The order service of the system is deployed in 3 instances, all providing services externally. Before a user places an order, they need to check the inventory. To prevent overselling, a lock is needed to synchronize access to the inventory check operation. Since the order service is located in different JVM processes, local locks cannot function properly in this case. We need to use distributed locks, which allow multiple threads, even if they are not in the same JVM process, to acquire the same lock, thereby achieving mutual access to shared resources.

Here is a diagram I created to illustrate distributed locks.

![Distributed Lock](https://oss.javaguide.cn/github/javaguide/distributed-system/distributed-lock/distributed-lock.png)

From the diagram, it can be seen that threads in these independent processes access shared resources mutually exclusively, with only one thread able to acquire the distributed lock to access the shared resource at any given time.

## What Conditions Should a Distributed Lock Meet?

A basic distributed lock needs to satisfy the following:

- **Mutual Exclusion**: At any given time, the lock can only be held by one thread.
- **High Availability**: The lock service is highly available. When one
