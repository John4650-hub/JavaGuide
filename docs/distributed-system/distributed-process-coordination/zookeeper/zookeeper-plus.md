I don't...
Title: ZooKeeper Related Conceptual Summary (Step Forward)
Category: Distribution
Tag:

- ZooKeeper
  I don't...

[FrancisQ](https://juejin.im/user/5c33853851882525ea106810)

What is ZooKeeper

`ZooKeeper` was developed by `Yahoo` and subsequently donated to `Apache`, and has now become the `Apache` top project. `ZooKeeper` is an open-source distributed application coordination server that provides consistency to distributed systems. Consistency is achieved through the `ZAB` agreement based on the `Paxos` algorithm. Its main functions include configuration maintenance, distributed synchronization, cluster management, etc.

In short, `ZooKeeper` is a **distributed framework for coordinated services**. Distributive? Coordinated services? What is this? Zenium

In fact, when it comes to the concept of distribution, I find that some of my classmates do not understand very well the concepts of distribution and cluster. I've been talking to my classmates about distribution. He said distribution is machine. One machine isn't enough for another. Of course, there is nothing wrong with adding machines. A distributed system must involve more than one machine, but remember, there's a similar concept in computer science: `Cluster`, and clusters are machine-added. But clustering and distribution are actually two completely different concepts.

For example, I now have a second kill service, and I can't handle it with too many singles, so I added a few servers to provide a second kill service, and this is the **Cluster**.

![cluster](https://oss.javaguide.cn/p3-juejin/60263e969b9e4a0f81724b1f4d5b3d58~tplv-k3u1fbpfffcp-zoom-1.jpeg)

But I'm going to do it in a different way now, and I'm going to do it in a second **split into sub-services, like creating order services, increasing points, decorating coupons, etc.** And then I'm going to put these sub-services on different servers, and this is **`Distributed`**.

![distributed](https://oss.javaguide.cn/p3-juejin/0d42e7b4249144b3a0c519216ae3d~tplv-k3u1fbpfcp-zoom-1.jpeg)

And why should I deny that the distribution is machine? Because I think the machine is more appropriate for cluster construction, because it's really just a machine. And in terms of distribution, you need to split your business first, then add machines (not just as simple as machines), and you have to solve a series of problems that distribution brings.

![image](https://oss.javaguide.cn/p3-juejin/e3662ca1a09c44b07f15df85c6ba8~tplv-k3u1fpffcp-zoom-1.jpeg)

For example, how the distributional components are coordinated, how to reduce the synergy between the systems, how to handle distributed services, how to configure the whole distributional system, etc. `ZooKeeper` addresses these problems primarily.

# Coherence

The design of a distributed system is bound to encounter a problem - **Because of the existence of a division tolerance (partition tolerance), we must have to weigh the system availability and data consistency.** This is the famous `CAP` theorem.

It is simple to understand, for example, a class as an entire system, and students as an independent subsystem. At this point, the little red-eyed girl in the class was discovered by the big-mouthed flower of the class, who was so excited to tell the people around, and then the news of the little red-eyed girl in the class. When the news spreads (dissemination), you catch a fellow student asking about them, and if you don't know, it means that there is a problem of data inconsistency throughout the class system (because Hana already knows the news). And if he doesn't answer you directly, the problem of system availability arises at a time when the entire class is spreading information (in order to ensure consistency, everyone needs to know to provide the service).

![image](https://oss.javaguide.cn/p3-juejin/38b9ff4b193e487fe32c9710c6d644~tplv-k3u1fbpfcp-zoom-1-203007171604318-202307160259975.jpeg)

The former is the approach of `Eureka`, which guarantees AP (availability), while the latter is the approach of `ZooKeeper`, which we are talking about today, which guarantees CP (data consistency).

# Consistency Protocols and Algorithms

In order to address the issue of data consistency, many consistency agreements and algorithms have emerged in the continuing exploration of scientists and programmers. For example, 2PC (submitted in two stages), 3PC (submitted in three stages), Paxos
