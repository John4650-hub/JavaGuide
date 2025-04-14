---
title: Detailed Explanation of the Gossip Protocol
category: Distributed
tag:
  - Distributed Protocols & Algorithms
  - Consensus Algorithms
---

## Background

In distributed systems, data/information sharing among different nodes is a fundamental requirement.

A relatively simple and straightforward method is **centralized message dissemination**, which essentially involves a primary node sharing the latest information with all other nodes simultaneously, making it more suitable for centralized systems. The drawbacks of this method are also quite evident; when there are many nodes, not only is the efficiency of message synchronization low, but it also heavily relies on the central node, posing a single point of failure risk.

Thus, the **Gossip Protocol** for **decentralized message dissemination** was born.

## Introduction to the Gossip Protocol

Gossip literally translates to idle talk or rumors. What are the characteristics of rumors? They are easily spread and propagate quickly; you tell me, I tell him, and then everyone knows.

![](./images/gossip/gossip.png)

The **Gossip Protocol** is also known as the Epidemic Protocol or Epidemic Propagation Algorithm, and it has many aliases. However, the common feature of these names is the **random propagation characteristic** (think of scenarios like virus transmission or cancer cell spread in everyday life), which is also the main feature of the Gossip Protocol.

The Gossip Protocol was first proposed in a paper published in 1987 in ACM titled [“Epidemic Algorithms for Replicated Database Maintenance”](https://dl.acm.org/doi/10.1145/41840.41841). From the title of the paper, we can infer that the main application of the Gossip Protocol at that time was to synchronize data among various replica nodes in distributed database systems.

As the name suggests, the Gossip Protocol is a random and contagious way to disseminate information throughout the network, ensuring that all nodes in the system achieve data consistency within a certain time frame.

Under the Gossip Protocol, there is no so-called central node; each node periodically and randomly selects another node to synchronize information with each other. Theoretically, the states of all nodes will eventually remain consistent.

To summarize the definition of the Gossip Protocol: **The Gossip Protocol is a decentralized communication protocol that allows state sharing in distributed systems, enabling us to disseminate information to all members in a network or cluster.**

## Applications of the Gossip Protocol

Notable projects such as the NoSQL databases Redis and Apache Cassandra, as well as service mesh solutions like Consul, utilize the Gossip Protocol. Learning about the Gossip Protocol helps us understand the underlying principles of many technologies.

Here, we will illustrate the practical application of the Gossip Protocol using Redis Cluster as an example.

The official clustering solution for the distributed cache Redis (introduced in version 3.0), Redis Cluster, is based on the Gossip Protocol to achieve eventual consistency of data among various nodes in the cluster.

![Official Clustering Solution of Redis](https://oss.javaguide.cn/github/javaguide/distributed-system/protocol/up-fcacc1eefca6e51354a5f1fc9f2919f51ec.png)

Redis Cluster is a typical distributed system where various nodes need to communicate with each other. Since communication is required, a consistent communication protocol must be followed. The nodes in Redis Cluster communicate and share information based on the **Gossip Protocol**, with each Redis node maintaining a copy of the cluster's state information.

Nodes in Redis Cluster send various Gossip messages to each other:

- **MEET**: Executing the `CLUSTER MEET ip port` command on a Redis node in Redis Cluster sends a MEET message to a specified Redis node, adding it as a new Redis node in the cluster.
- **PING/PONG**: Nodes in Redis Cluster periodically send PING messages to other nodes to exchange status information, checking the status of each node, including online status, suspected offline status (PFAIL), and offline status (FAIL).
- **FAIL**: If node A in Redis Cluster detects that node B is PFAIL, and more than half of the nodes in the cluster mark B as PFAIL within the validity period of the offline report, node A will broadcast a FAIL message to the cluster, notifying other nodes to mark the faulty node B as FAIL.
- ……

The diagram below illustrates the master-slave architecture of Redis Cluster, where the dashed lines represent communication between nodes using Gossip, and the solid lines indicate master-slave replication.

![](./images/gossip/redis-cluster-gossip.png)

With Redis Cluster, there is no need to deploy a separate Sentinel cluster service. Redis Cluster effectively has a built-in Sentinel mechanism, with the various Redis nodes internally sharing cluster information through the Gossip Protocol.

For a detailed introduction to Redis Cluster, you can refer to this article [Detailed Explanation of Redis Cluster (Paid)](https://javaguide.cn/database/redis/redis-cluster.html).

## Gossip Protocol Message Propagation Modes

Gossip has designed two possible message propagation modes: \*\*Anti-Entropy
