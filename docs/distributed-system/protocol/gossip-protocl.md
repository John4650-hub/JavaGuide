I don't...
Title: Gossip Protocol Details
Category: Distribution
Tag:

- Distributive protocols & algorithms
- Consensus algorithm
  I don't...

# Background

In distributed systems, data/information sharing at different nodes is a basic requirement.

One of the simpler and more viable methods is **centralized information**, simply a main node that shares up-to-date information with all other nodes, which is better suited to a centralized system. The shortcomings of this approach are also evident, as there are many nodes that are less efficient than synchronized messages and are too dependent on the nodes, leading to single-point risks.

So, the **Gossip protocol** was born.

# Gossip Protocol Introduction

Gossip translates directly into gossip and chatter. What are the characteristics of gossip? It's easy to spread and it's fast. You call me, and I call him, and everybody knows.

![./images/gossip/gossip.png]

**Gossip protocol** is also known as the Epidemic protocol or the Epidemic protocol algorithm, with many aliases. However, these names are all characterized by **random transmission** (reminiscent of common life situations such as the spread of a virus and the spread of cancer cells), which is the most important feature of the Gossip protocol.

The Gossip protocol was first presented in a paper published in 1987 on ACM [Epidemic Algorithms for Replicated Data Management](https://dl.acm.org/doi/10.1145/41840.41841). According to the title of the paper, we would probably know that the main application that the Gossip protocol proposed at the time was to synchronize data at the various copy nodes in the distributed database system.

As in the Gossip protocol, it is a random and contagious way of disseminating information across the network and for a certain period of time, ensuring consistent data across all nodes of the system.

Under the Gossip protocol, there is no so-called central node, and each node is randomly searched for information on a regular basis to synchronize with each other, and theoretically, the state of each node will eventually be consistent.

Let us summarize the definition of the Gossip protocol: **Gossip is a decentralized communication protocol that allows sharing in a distributed system, through which we can disseminate information to all members of the network or cluster.**

# Gossip Protocol Application

The NoSQL database Redis and Apache Cassandra, and the Service Grid Solutions Consul use Gossip protocols to help us understand the underlying principles of many technologies.

We're here to illustrate the practical application of the Gossip protocol.

The distributed cache of Redis' official cluster solution (version 3.0 introduced) which we often use is the ultimate consistency of nodes in the cluster based on Gossip protocols.

Official cluster solutions in [Redis](https://oss.javaguide.cn/github/javaguide/distributed-system/protocol/up-fcacc1eefca6e513554a5f1ff9f2919f51ec.png)

Redis Cluster is a typical distributed system in which nodes need to communicate with each other. Since communication requires consistent communication protocols, each node in Redis Cluster is based on **Gossip protocol** to communicate and share information, and each Redis node maintains cluster status information.

There are multiple Gossip messages between Redis Cluster nodes:

- **MEET**: Execute the `CLUSTER MEET ip port` command at one of the Redis Cluster Nodes. You can send a MEET message to the specified Redis Node to add it to the Redis Cluster as a new Redis Node.
- **PING/PONG**: PING messages are sent regularly to other nodes in the Redis Cluster to exchange information on the state of each node and check the state of each node, including online, suspected offline (PFAIL), and downed (FAIL).
- **FAIL**: Node A in Redis Cluster finds that Node B is PFAIL and marks more than half the cluster nodes as PFAIL during the useful reporting period, and Node A broadcasts a FAIL message to the cluster, informing other nodes that Node B will be marked as FAIL.
- ...

The following is a diagram of the main structure of the Redis Cluster, where the dotted lines represent communications between the nodes using Gossip, and the solid lines represent the main copy.

![./images/gossip/redis-cluster-gossip.png](./images/gossip/redis-cluster-gossip.png)

With Redis Cluster, there is no need for a dedicated Sentinel cluster service. The Redis Cluster has a built-in Sentinel mechanism, and the Redis nodes within Redis Cluster share information within the cluster through Gossip protocols.

For a detailed description of Redis Cluster, see the article [Redis Cluster Details](https://javaguide.cn/database/redis/redis-cluster.html).

# Gossip Protocol Message Dissemination Mode

Gossip has designed two possible modes of dissemination: **Anti-Entropy** and **Rumor-Mongering**.

# Anti-Entropy

According to Wikipedia:

> The concept of entropy originated in [physics](https://zh.wikipedia.org/wiki/physics) to measure the degree of disorder of a thermodynamic system.
