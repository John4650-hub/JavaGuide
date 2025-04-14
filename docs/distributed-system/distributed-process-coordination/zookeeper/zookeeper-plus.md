---
title: Summary of ZooKeeper Related Concepts (Advanced)
category: Distributed
tag:
  - ZooKeeper
---

> [FrancisQ](https://juejin.im/user/5c33853851882525ea106810) Contribution.

## What is ZooKeeper

`ZooKeeper` was developed by `Yahoo` and later donated to `Apache`, and it has now become an Apache top-level project. `ZooKeeper` is an open-source distributed application coordination server that provides consistency services for distributed systems. Its consistency is achieved through the `ZAB` protocol based on the `Paxos` algorithm. Its main functions include configuration maintenance, distributed synchronization, cluster management, and more.

In simple terms, `ZooKeeper` is a **distributed coordination service framework**. Distributed? Coordination service? What is that? 🤔🤔

When explaining the concept of distribution, I found that some students do not fully understand the difference between **distributed systems and clusters**. Recently, a student discussed distributed systems with me, saying that isn't distributed just about adding machines? If one machine is not enough, just add another to handle the load. Of course, saying "add machines" is not entirely wrong; a distributed system must involve multiple machines. However, don't forget that there is a similar concept in computer science—`Cluster`. Isn't a cluster also about adding machines? But clusters and distributed systems are actually two completely different concepts.

For example, I now have a flash sale service, and the concurrency is too high for a single machine to handle. If I add a few servers, I can still provide the flash sale service; this is a **`Cluster`**.

![cluster](https://oss.javaguide.cn/p3-juejin/60263e969b9e4a0f81724b1f4d5b3d58~tplv-k3u1fbpfcp-zoom-1.jpeg)

However, if I take a different approach and **split a flash sale service into multiple sub-services**, such as creating an order service, a points service, a coupon deduction service, etc., **and then deploy these sub-services on different servers**, this is a **`Distributed`** system.

![distributed](https://oss.javaguide.cn/p3-juejin/0d42e7b4249144b3a77a0c519216ae3d~tplv-k3u1fbpfcp-zoom-1.jpeg)

Why do I refute the student's claim that distributed means just adding machines? Because I believe that adding machines is more applicable to building a cluster, as it truly only involves adding machines. In contrast, for a distributed system, you first need to split the business, and then add machines (it's not just as simple as adding machines). At the same time, you also need to solve a series of problems brought by distribution.

![](https://oss.javaguide.cn/p3-juejin/e3662ca1a09c4444b07f15dbf85c6ba8~tplv-k3u1fbpfcp-zoom-1.jpeg)

For example, how various distributed components coordinate, how to reduce coupling between systems, handling distributed transactions, how to configure the entire distributed system, etc. `ZooKeeper` mainly addresses these issues.

## Consistency Issues

Designing a distributed system will inevitably encounter a problem—**due to the existence of partition tolerance, we must make trade-offs between system availability and data consistency**. This is the famous `CAP` theorem.

Understanding it is actually quite simple. For example, consider a class as the entire system, and the students are independent subsystems within the system. At this point, if Xiaohong and Xiaoming in the class are secretly dating and are discovered by the gossiping Xiaohua, who excitedly tells those around her, the news of Xiaohong and Xiaoming's relationship spreads throughout the class. If you catch a student during the spread of the news and ask them about it, if they respond with "I don't know," it indicates that the entire class system has a data inconsistency issue (because Xiaohua already knows this news). If they simply do not answer you, it means that the entire class is in the process of spreading the news (to ensure consistency, everyone must know before services can be provided), and at this point, a system availability issue arises.

![](https://oss.javaguide.cn/p3-juejin/38b9ff4b193e4487afe32c9710c6d644~tplv-k3u1fbpfcp-zoom-1-20230717160254318-20230717160259975.jpeg)

The former is the handling method of `Eureka`, which guarantees AP (availability), while the latter is the handling method of `ZooKeeper`, which guarantees CP (data consistency).

## Consistency Protocols and Algorithms

To solve the data consistency problem, many consistency protocols
