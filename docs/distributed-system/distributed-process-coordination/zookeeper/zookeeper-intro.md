I don't...
Title: ZooKeeper Related Conceptual Summary (Introduction)
Category: Distribution
Tag:

- ZooKeeper
  I don't...

I'm sure ZooKeeper is not a stranger. But do you really know ZooKeeper? What's the point? What would you say if someone asked you to tell him about ZooKeeper?

Take me! I myself used Dubbo in college to do distributed projects, using ZooKeeper as a registration center. In order to ensure that the distribution system has simultaneous access to a resource, I also made a distribution lock using ZooKeeper. Also, when I was studying Kafka, I knew that Kafka was dependent on ZooKeeper for a lot of functionality.

The other day, when I learned from the project, I suddenly asked myself, "What is ZooKeeper?" After half a day of thinking, there were just a few words in my head:

1. ZooKeeper may be used as a registration center and distributed lock;
1. ZooKeeper is a member of the Hadoop ecosystem;
1. In the construction of the ZooKeeper cluster, it would be preferable to use an odd number of servers.

So my understanding of ZooKeeper is just staying on the surface.

By this, I would like to show you a little bit more about ZooKeeper. If you don't learn ZooKeeper, then this will be the building blocks for you to enter the ZooKeeper gate. If you have already reached ZooKeeper, this paper will take you back to some of the underlying concepts of ZooKeeper.

In addition, this paper will not only cover some of the concepts of ZooKeeper but will be followed by articles on the use of common ZooKeeper commands and the use of Apache Curator as ZooKeeper's client.

*If there's anything in the articles that needs to be improved and perfected, we welcome comments in the comment section for common progress!*

# ZooKeeper Introduction

## ZooKeeper

Before officially introducing ZooKeeper, it's interesting to look at the history of ZooKeeper.

The following passage is taken from Chapter IV, Section 1, of "Paxos to ZooKeeper" and is recommended for reading:

> ZooKeeper originally originated in a research team at the Yahoo Institute. At that time, researchers found that many large systems within Yahoo were largely dependent on a similar system for distributed coordination, but that these systems often had distributed single-point problems. As a result, Yahoo's developers tried to develop a generic, distributed coordination framework for single-point issues in order to focus their efforts on business logic.

There is also an interesting story about the name of the project “ZooKeeper.” In the early stages of the project, Yahoo's engineers wished to give the project an animal's name, considering that many of the previous projects in the interior had been named after animals (for example, the famous Pig project). Raghu Ramakrishnan, the chief scientist of the Institute at the time, joked, "In this way, we will become zoos!" As soon as you say this, you call the zoo manager because the distribution components named after the animals are put together, Yahoo's entire distribution system looks like a big zoo, and ZooKeeper is just about to be used to coordinate the distributed environment, and ZooKeeper's name is born.

# ZooKeeper Overview

ZooKeeper is an open-source **distributed coordination service** designed to encapsulate those distribution consistency services that are complex and prone to error, to constitute an efficient and reliable set of original languages and to make them available to users through a series of simple and user-friendly interfaces.

> **Original:** Terms of reference for operating systems or computer networks. It is a process consisting of several directives and used to complete certain functions. Indivisibility, i.e., the implementation of the original language must be continuous and cannot be interrupted in the course of implementation.

ZooKeeper provides us with high availability, high performance, and stable distributed data consistency solutions, which are often used to achieve functions such as data dissemination/subscription, load balancing, naming services, distributed coordination/notification, cluster management, master elections, distributed locks, and distributed queues. The performance of these functions is largely dependent on the **data storage + event listening** function provided by ZooKeeper (discussed later).

ZooKeeper saves data in memory with good performance, particularly high performance in applications that read more than write, as writing leads to synchronization between all servers. (Read more than write is a typical scenario for coordinating services.)

In addition, many top-level open-source projects have used ZooKeeper, such as:

- **Kafka**: ZooKeeper mainly provides Broker and Topic registrations and load balances for Kafka and multiple Partitions. However, after Kafka 2.8, the KRaft model based on the Raft protocol was introduced, and the structure of Kafka was considerably simplified by not relying on ZooKeeper.
- **HBase**: ZooKeeper provides HBase with features such as ensuring that there is only one Master for the entire cluster and the preservation and provision of region server status information (whether online or not).
- **Hadoop**: ZooKeeper provides highly
