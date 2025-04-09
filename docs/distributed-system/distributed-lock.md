I don't...
title: Distribution Lock Introduction
Category: Distribution
I don't...

@include: @small-advertition.snippet.md-->

There are many articles on the Internet about distributed locks and a relatively simple and understandable version, which should be sufficient for interviews and work.

Let's start with the basic concept of a distributed lock.

Why do you need a distributed lock?

In a multi-threaded environment, where multiple threads access shared resources at the same time (e.g., commodity inventories, overload orders), data competition can occur and may lead to dirty data or systemic problems that threaten the proper functioning of the process.

As an example, it is assumed that 100 users are now involved in a time-bound sale, that one commodity is limited to each user and that there are only three. If there is no cross-examination of shared resources, it is possible that:

- Multiple threads (1, 2, and 3) go into the acquisition method at the same time, each corresponding to one user.
- Thread 1 surveys the number of acquisitions already made by users. It finds that the current user has not yet made a purchase and that there is one in the inventory of commodities, and it is therefore considered possible to continue the process.
- Thread 2 also carries out inquiries into the number of acquisitions that have been made by the users, and it finds that the current user has not yet made a purchase and that there is one more in the inventory of commodities, so it is considered possible to continue the process.
- Thread 1 continues, reducing the stock by 1 and then returning success.
- Thread 2 continues, reducing the stock by 1 and then returning successfully.
- The problem of oversale occurs at that time, resulting in the sale of one additional commodity.

[Sharing resources without mutually exclusive visits leads to problems](https://oss.javaguide.cn/github/javaguide/distributed-system/distributed-lock/oversold-without-locking.png)

In order to ensure that shared resources are safely accessed, we need to protect shared resources using a mutually exclusive approach, which allows only one thread to access shared resources at the same time, while other threads await the release of the current thread. This would avoid data competition and dirty data problems and ensure the correctness and stability of the process.

**How can mutually exclusive access to shared resources be achieved?** A lock is a more common solution and, more precisely, a pessimistic lock.

The pessimistic lock always assumes that the worst-case scenario is that every time a shared resource is visited, problems arise (e.g., when the shared data is modified), so every time a resource is accessed, the lock is acquired, blocking other threads that want to access the resource until the lock is released by the previous holder. That is to say, **the shared resources are given to only one thread at a time; other threads are blocked and then transferred to the other.**

For a single multi-threaded environment, in Java, we usually use the `ReentrantLock` class and `synchronized` keywords like JDK's own **local lock** to control access to local shared resources by multiple threads within a JVM process.

Here's a map of my local lock.

![local lock](https://oss.javaguide.cn/github/javaguide/distributed-system/distributed-lock/jvm-local-lock.png)

As can be seen from the graph, these threads accessing shared resources are mutually exclusive, with only one thread available at the same time for local lock access to shared resources.

Under the distributed system, different services/clients usually operate on separate JVM processes. If multiple JVM processes share the same resource, the use of local locks does not allow for mutually exclusive access. So, **distributed locks** were born.

For example, three order services were deployed in the system, all of which were external. In order to prevent oversale, the inventory needs to be checked before the user orders, and locking is required to allow simultaneous access to the inventory operations. As the order services are located in different JVM processes, local locks cannot work properly in this situation. We need to use distributed locks, so that even multiple threads do not get the same locks in the same JVM process, thus achieving mutually exclusive access to shared resources.

Here's a diagram of my distributed lock-ups.

![distributed lock](https://oss.javaguide.cn/github/javaguide/distributed-system/distributed-lock/distributed-lock.png)

As can be seen from the graphs, the threads accessing shared resources in these separate processes are mutually exclusive, with only one thread at the same time available for distributed access to shared resources.

What are the conditions for a distributed lock?

A basic distributed lock needs to meet the following conditions:

- **Mutual exclusion**: At any given moment, the lock can only be held by one thread.
- **High availability**: Locking services are highly available and can automatically switch to another lock service when a lock service is in trouble. Furthermore, even if there is a problem with the code logic of the release lock on the client
