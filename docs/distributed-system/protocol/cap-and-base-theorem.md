---
title: Explanation of CAP & BASE Theories
category: Distributed Systems
tag:
  - Distributed Theory
---

Those who have experienced technical interviews are likely very familiar with the CAP & BASE theories!

When I participated in interviews back then, I can say without exaggeration that as soon as distributed-related topics came up, interviewers almost invariably asked about these two distributed theories. This is partly because these two foundational theories are essential prerequisites for learning about distributed systems, and partly because many interviewers themselves are familiar with these theories (making it easier to ask questions).

It is very necessary for us to understand these two theories and be able to explain them to others in our own words.

## CAP Theory

[CAP Theorem](https://zh.wikipedia.org/wiki/CAP%E5%AE%9A%E7%90%86) originated in 2000, proposed by Professor Eric Brewer from the University of California, Berkeley at the Principles of Distributed Computing (PODC) symposium. Therefore, the CAP theorem is also known as **Brewer's theorem**.

Two years later, Seth Gilbert and Nancy Lynch from MIT published a proof of Brewer's conjecture, and the CAP theory officially became a theorem in the distributed field.

### Introduction

**CAP** is a combination of the first letters of **Consistency**, **Availability**, and **Partition Tolerance**.

![](https://oss.javaguide.cn/2020-11/cap.png)

When Brewer proposed the CAP conjecture, he did not provide a detailed definition of **Consistency**, **Availability**, and **Partition Tolerance**.

As a result, there are many informal interpretations of CAP, with the most commonly recommended interpretation being the one below 👇.

In theoretical computer science, the CAP theorem states that for a distributed system, when designing read and write operations, it can only simultaneously satisfy two of the following three points:

- **Consistency**: All nodes access the same up-to-date data copy.
- **Availability**: Non-faulty nodes return reasonable responses (not errors or timeouts) within a reasonable time.
- **Partition Tolerance**: The system continues to provide services despite network partitions.

**What is a network partition?**

In a distributed system, multiple nodes are originally connected, but due to some faults (like issues with part of the node's network), some nodes become unreachable, and the entire network splits into several regions. This is called a **network partition**.

![partition-tolerance](https://oss.javaguide.cn/2020-11/partition-tolerance.png)

### Not a "choose 2 of 3"

Most people simplistically explain this theorem by saying: "You can only achieve two of consistency, availability, and partition tolerance at the same time, and cannot achieve all three." This is actually a very misleading statement, and twelve years after the CAP theory was born, its father also rewrote his earlier paper in 2012.

> **When a network partition occurs, if we are to continue providing services, then strong consistency and availability can only be chosen between 2 out of 1. In other words, when a network partition happens, P is the prerequisite, determining the choice of C or A thereafter. This means partition tolerance (P) is something we must implement.**
>
> In short: In the CAP theory, partition tolerance P must be satisfied, and on this basis, we can only satisfy either availability A or consistency C.

Therefore, **theoretically, a distributed system cannot choose a CA architecture but can only choose a CP or AP architecture.** For example, ZooKeeper and HBase are CP architectures, while Cassandra and Eureka are AP architectures. Nacos supports both CP and AP architectures.

**Why is it impossible to choose a CA architecture?** For example: If a "partition" occurs in the system and a certain node is performing a write operation. To ensure C, we must prohibit other nodes’ read and write operations, which conflicts with A. Conversely, if we ensure A and allow other nodes to perform read and write operations, that would conflict with C.

**The choice between CP and AP depends on the current business scenario, with no conclusive answer. For scenarios requiring strong consistency, such as banking, CP is generally chosen.**

Additionally, it should be noted that: **If the network partition is functioning normally (which is the state the system is in most of the time), that is, when P does not need to be ensured, C and A can be guaranteed simultaneously.**

### Practical Application of CAP

I will explore the practical application of CAP using a registration center. Considering many may not know what a registration center is for, I will briefly explain it using Dubbo as an example.

The following diagram is the architecture of Dubbo. **What role does the registration center play? What services does it provide?**

The registration center is responsible for the registration and discovery of service addresses, analogous to a directory service. Service providers and consumers only interact with the registration center upon starting, and the registration center does not forward requests, resulting in low pressure.

![](https://oss.javaguide.cn/2020-11/dubbo-architecture.png)

Common components that can serve as registration centers include: ZooKeeper, Eureka, Nacos...

1. **ZooKeeper guarantees CP.** Any read request to ZooKeeper will provide a consistent result; however, ZooKeeper does not guarantee availability during events like Leader election or when more than half of the machines are unavailable—services would then be unavailable.
1. **Eureka guarantees AP.** Eureka was designed with priority on A (availability). There is no Leader node in Eureka; each node is the same and equal. Thus, unlike ZooKeeper, Eureka does not face unavailability during election processes or when more than half of the machines are unavailable. Eureka ensures that as long as one node is available, normal services can continue, even if that node's data may not be the latest.
1. **Nacos supports both CP and AP.**

**🐛 Correction (see: [issue#1906](https://github.com/Snailclimb/JavaGuide/issues/1906))**:

ZooKeeper ensures data consistency through linearizable writes, global FIFO access, and other mechanisms. In a multi-node deployment, the ZooKeeper cluster operates in Quorum mode. In this mode, a group of ZooKeeper server nodes is formed, where the majority must agree to any changes for them to be considered valid.

Due to the nature of Quorum mode, read requests do not trigger data synchronization between different ZooKeeper nodes, resulting in situations where outdated data may still be read, leading to inconsistent views on different clients due to reasons like network latency, packet loss, or retransmission. To address this, ZooKeeper provides a Watcher mechanism and versioning to help clients detect changes in data and version changes to ensure data consistency.

### Conclusion

In designing and developing distributed systems, we should not be limited to the CAP issue but also focus on aspects such as system scalability and availability.

In the event of a network "partition", the CAP theorem can only satisfy CP or AP. It is important to note that this assumes a "partition" has occurred.

If there is no "partition" in the system and the network communication among nodes is normal, P no longer exists. At that point, we can simultaneously ensure C and A.

In summary: **If a partition occurs in the system, we must consider whether to choose CP or AP. If there is no partition, we should think about how to ensure CA.**

### Recommended Reading

1. [Simplifying the CAP Theorem](https://medium.com/@ravindraprasad/cap-theorem-simplified-28499a67eab4) (English, with interesting examples)
1. [Where is the Amazing CAP Theory Applied](https://juejin.im/post/6844903936718012430) (Chinese, listing many practical examples)
1. [Please Stop Calling Databases CP or AP](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html) (English, offering a different perspective)

## BASE Theory

[BASE Theory](https://dl.acm.org/doi/10.1145/1394127.1394128) originated in 2008 and was published by eBay architect Dan Pritchett in ACM.

### Introduction

**BASE** is an acronym for **Basically Available**, **Soft-state**, and **Eventually Consistent**. The BASE theory is the result of the trade-offs between consistency (C) and availability (A) in CAP, arising from a summary of distributed practices in large-scale Internet systems. It has gradually evolved from the CAP theorem, significantly relaxing our requirements of the system.

### Core Idea of BASE Theory

Even if strong consistency cannot be achieved, every application can adopt appropriate methods to achieve eventual consistency according to its business characteristics.

> This means sacrificing data consistency to ensure high system availability, wherein part of the system's data may be unavailable or inconsistent, yet the system overall still remains "mostly available".

**The BASE theory is essentially an extension and supplement to CAP, specifically, it supplements the AP solution in CAP.**

**Why do I say this?**

As mentioned in the section on the CAP theory:

> If the system has not undergone a "partition," and the network connections between nodes are functioning normally, then P does not exist. At that point, we can guarantee both C and A. Thus, **if a partition occurs in the system, we must consider choosing between CP or AP. If there is no "partition," we should think about how to ensure CA.**

Therefore, the AP solution only sacrifices consistency during system partitions but does not permanently relinquish consistency. After recovering from partition faults, the system should achieve eventual consistency. This is, in fact, the essence of the extension provided by the BASE theory.

### Three Elements of BASE Theory

![Three Elements of BASE Theory](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOC81LzI0LzE2MzkxNDgwNmQ5ZTE1YzY?x-oss-process=image/format,png)

#### Basically Available

Basically available means that when unexpected failures occur in a distributed system, it allows some loss of availability. However, this does not equate to the system being unavailable.

**What does it mean to allow loss of availability?**

- **Loss in response time**: Normally, it takes 0.5s to handle a user request, but due to system failures, the response time extends to 3s.
- **Loss in system functionality**: Normally, users can access all features of the system, but due to a sudden surge in traffic, some non-core functionalities may become unavailable.

#### Soft State

Soft state refers to allowing data in the system to exist in intermediate states (**data inconsistency as described in the CAP theory**) while recognizing that the existence of this intermediate state will not affect the overall availability of the system. This means allowing delays in the synchronization process of data replicas between different nodes.

#### Eventually Consistent

Eventually consistent emphasizes that all data replicas in the system can achieve a consistent state after a period of synchronization. Therefore, the essence of eventual consistency is that the system needs to ensure that the final data can achieve consistency, rather than requiring real-time strong consistency of the data.

> The three levels of distributed consistency:
>
> 1. **Strong consistency**: What is written in the system is exactly what is read.
> 1. **Weak consistency**: It is not certain that the most recently written value can be read, nor is there a guarantee that the data read after a certain time will be the latest; it only aims to achieve data consistency at a given moment.
> 1. **Eventual consistency**: This is an upgraded version of weak consistency, wherein the system guarantees that data will achieve consistency within a certain period.
>
> **The industry tends to commend the eventual consistency level, but in certain scenarios where data consistency is critically required, such as banking transactions, strong consistency must still be guaranteed.**

What are the specific ways to achieve eventual consistency? In [《Distributed Protocols and Algorithms in Practice》](http://gk.link/a/10rZM), it is introduced as follows:

> - **Read Repair**: During data reads, inconsistencies are detected and repaired. For example, Cassandra's Read Repair implementation automatically fixes data when inconsistencies are found among the replicas during a read query.
> - **Write Repair**: During data writes, inconsistencies are detected and repaired. For example, Cassandra's Hinted Handoff implementation caches data that fails to write remotely between cluster nodes and retransmits it periodically to fix the inconsistencies.
> - **Asynchronous Repair**: This is the most commonly used method, conducted through regular reconciliation checks to verify the consistency of replica data and repair it.

**Write Repair** is highly recommended as this method is less demanding on performance.

### Conclusion

**ACID is a theory of transaction integrity in databases, CAP is a theory of distributed system design, and BASE is an extension of the AP solution in CAP theory.**

<!-- @include: @article-footer.snippet.md -->
