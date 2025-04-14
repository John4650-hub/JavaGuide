---
title: Summary of ZooKeeper Related Concepts (Introduction)
category: Distributed
tag:
  - ZooKeeper
---

I believe everyone is somewhat familiar with ZooKeeper. But do you really understand what ZooKeeper is used for? If someone, like an interviewer, asks you to explain your understanding of ZooKeeper, how far could you go in your answer?

Let me share my own experience! During my university days, I used Dubbo for a distributed project and employed ZooKeeper as the registration center. To ensure that the distributed system could synchronously access a certain resource, I also used ZooKeeper for distributed locking. Additionally, while learning Kafka, I realized that many of Kafka's functionalities depend on ZooKeeper.

A few days ago, while summarizing project experiences, I suddenly asked myself, what exactly is ZooKeeper? After thinking for a long time, I could only recall a few simple statements:

1. ZooKeeper can be used as a registration center and for distributed locks;
1. ZooKeeper is a part of the Hadoop ecosystem;
1. When building a ZooKeeper cluster, it is best to use an odd number of servers.

From this, it is clear that my understanding of ZooKeeper was merely superficial.

Therefore, through this article, I hope to provide a more detailed understanding of ZooKeeper. If you have never learned about ZooKeeper, this article will serve as a stepping stone for you to enter the world of ZooKeeper. If you have already been exposed to ZooKeeper, this article will help you review some basic concepts.

Moreover, this article will not only cover some concepts of ZooKeeper; subsequent articles will introduce common commands for ZooKeeper and the use of Apache Curator as a client for ZooKeeper.

_If there are any areas in the article that need improvement or enhancement, please feel free to point them out in the comments section for our mutual progress!_

## Introduction to ZooKeeper

### Origin of ZooKeeper

Before formally introducing ZooKeeper, let's take a look at its origin, which is quite interesting.

The following excerpt is from Chapter 4, Section 1 of "From Paxos to ZooKeeper," and I recommend everyone read it:

> ZooKeeper originated from a research group at Yahoo! Research. At that time, researchers found that many large systems within Yahoo! relied on a similar system for distributed coordination, but these systems often had distributed single point issues. Therefore, Yahoo! developers attempted to create a general-purpose distributed coordination framework without single points of failure, allowing developers to focus on business logic.
>
> Regarding the name "ZooKeeper," there is also an interesting story. In the early stages of the project, considering that many previous internal projects were named after animals (such as the famous Pig project), Yahoo! engineers wanted to give this project an animal name as well. The then chief scientist of the research institute, Raghu Ramakrishnan, jokingly said, "At this rate, we will turn into a zoo!" Once this was said, everyone agreed to call it ZooKeeper—because various distributed components named after animals together made Yahoo!'s entire distributed system look like a large zoo, and ZooKeeper was just meant for coordinating in a distributed environment—thus, the name ZooKeeper was born.

### Overview of ZooKeeper

ZooKeeper is an open-source **distributed coordination service** designed to encapsulate complex and error-prone distributed consistency services into a set of efficient and reliable primitives, providing users with a series of simple and easy-to-use interfaces.

> **Primitive:** A term used in operating systems or computer networks. It is a process composed of several instructions used to accomplish a certain function. It has indivisibility, meaning that the execution of a primitive must be continuous and cannot be interrupted during execution.

ZooKeeper provides us with a high-availability, high-performance, and stable distributed data consistency solution, commonly used to implement functionalities such as data publishing/subscription, load balancing, naming services, distributed coordination/notification, cluster management, master election, distributed locks, and distributed queues. The implementation of these functionalities mainly relies on the **data storage + event listening** capabilities provided by ZooKeeper (which will be detailed later).

ZooKeeper stores data in memory, resulting in good performance, especially in applications where "reads" outnumber "writes," as "writes" cause synchronization of states among all servers. (The scenario of "reads" outnumbering "writes" is typical for coordination services).

Additionally, many top open-source projects utilize ZooKeeper, such as:

- **Kafka**: ZooKeeper primarily provides registration for Brokers and Topics, as well as load balancing for multiple Partitions. However, after Kafka 2.8, it introduced the KRaft mode based on the Raft protocol, eliminating the dependency on ZooKeeper and greatly simplifying Kafka's architecture.
- **HBase**: ZooKeeper ensures that there is only one Master in the entire HBase cluster and provides and saves the status information of region servers (whether they are online).
- **Hadoop**: ZooKeeper provides high availability support for the Namen
