---
title: Detailed Explanation of the Paxos Algorithm
category: Distributed
tag:
  - Distributed Protocols & Algorithms
  - Consensus Algorithms
---

## Background

The Paxos algorithm is a consensus algorithm for distributed systems proposed by Leslie Lamport in **1990**. It is also the first consensus algorithm to be proven to be complete (under the assumption that the Byzantine Generals problem does not exist, meaning there are no malicious nodes).

To introduce the Paxos algorithm, Lamport wrote a humorous and witty paper. In this paper, he created a fictional Greek city-state called Paxos to more vividly illustrate the Paxos algorithm.

However, the reviewers did not appreciate the humor in the paper. They told Lamport, "If you want to successfully publish this paper, you must remove all the background stories related to Paxos." Lamport was not pleased and replied, "Why should I make changes? You reviewers are simply lacking a sense of humor. If you can't publish it, then do not publish it!"

As a result, the paper proposing the Paxos algorithm was not successfully published at that time.

It wasn't until **1998** that two technical researchers from the Systems Research Center (SRC) needed to find suitable distributed algorithms for the distributed system they were building, and the Paxos algorithm happened to fulfill some of their requirements. Lamport then sent the paper to them. After reviewing the paper, these two researchers found it to be quite impressive. Thus, Lamport republished his paper titled [“The Part-Time Parliament”](http://lamport.azurewebsites.net/pubs/lamport-paxos.pdf) in **1998**.

After the paper was published, scholars around the world exclaimed that it was hard to understand, with a hint of mocking tone in their words. Who could stand that? In **2001**, Lamport specifically wrote another paper [“Paxos Made Simple”](http://lamport.azurewebsites.net/pubs/paxos-simple.pdf) to simplify the introduction of Paxos, mainly discussing the two-phase consensus protocol, while also taking a dig at those scholars.

The paper "Paxos Made Simple" is only 14 pages long, considerably shorter than the 33 pages of "The Part-Time Parliament." The key point is that the abstract of this paper is just one sentence:

![](./images/paxos/paxos-made-simple.png)

> The Paxos algorithm, when presented in plain English, is very simple.

This roughly translates to: "When I describe it in straightforward English, the Paxos algorithm is genuinely simple!"

Do you sense the strong flavor of sarcasm from Lamport?

## Introduction

The Paxos algorithm is the first proven comprehensive consensus algorithm for distributed systems. The role of a consensus algorithm is to reach a unanimous opinion among multiple nodes in a distributed system about a certain proposal (Proposal). The meaning of a proposal in a distributed system is quite broad, encompassing which node is the Leader, the order of multiple events, etc.

The Paxos algorithm proposed by Lamport actually comprises 2 parts:

- **Basic Paxos Algorithm**: This describes how multiple nodes can reach a consensus on a certain value (proposed Value).
- **Multi-Paxos Concept**: This describes executing multiple Basic Paxos instances to achieve consensus on a series of values. Multi-Paxos, simply put, entails executing Basic Paxos multiple times, with the core still being Basic Paxos.

Due to the international consensus that the Paxos algorithm is particularly difficult to understand and implement, continuous attempts have been made to simplify this algorithm. It wasn't until 2013 that a consensus algorithm more understandable and implementable than Paxos—the [Raft algorithm](https://javaguide.cn/distributed-system/theorem&algorithm&protocol/raft-algorithm.html)—was born. More specifically, Raft is a variant of Multi-Paxos that simplifies the ideas behind Multi-Paxos, making it easier to understand and engineer.

For scenarios without malicious nodes, some commonly used consensus algorithms, like the **ZAB protocol**, **Fast Paxos**, are all improvements based on the Paxos algorithm.

In cases with malicious nodes, consensus algorithms like **Proof-of-Work (PoW)** and **Proof-of-Stake (PoS)** are generally used. A typical application of such consensus algorithms is in blockchain technology. For example, recently, Ethereum's official announcement indicated that its consensus mechanism is transitioning from Proof-of-Work (PoW) to Proof-of-Stake (PoS).

The core problem that consensus algorithms used in blockchain systems need to solve is the **Byzantine Generals problem**, which is quite different from the distributed middleware we encounter in daily life, such as ZooKeeper, Etcd, and Consul.

Now, let's summarize the definition of the Paxos algorithm:

- The Paxos algorithm is a consensus algorithm for distributed systems proposed by Lamport in **1990**.
- The Paxos algorithm proposed by Lamport primarily consists of 2 parts: the Basic Paxos algorithm and the Multi-Paxos concept.
- The Raft algorithm, ZAB protocol, and Fast Paxos algorithm are all derived from improvements over the Paxos algorithm.

## Basic Paxos Algorithm

The Basic Paxos algorithm involves 3 important roles:

1. **Proposer**: Also known as the Coordinator, the proposer is responsible for accepting client requests and initiating proposals. Proposal information typically includes a proposal number (Proposal ID) and the proposed value (Value).
1. **Acceptor**: Also referred to as a voter, the acceptor is responsible for voting on proposals from the proposer and must remember its voting history.
1. **Learner**: If more than half of the acceptors reach a consensus on a certain proposal, the learner must accept that proposal, perform computations based on it, and then return the computation results to the client.

![](https://oss.javaguide.cn/github/javaguide/distributed-system/protocol/up-890fa3212e8bf72886a595a34654918486c.png)

To reduce the number of nodes required to implement this algorithm, a single node can take on multiple roles. Additionally, a proposal must be accepted by more than half of the acceptors to be chosen. This way, the Basic Paxos algorithm also possesses fault tolerance, allowing the cluster to operate normally even when less than half of the nodes fail.

## Multi-Paxos Concept

The Basic Paxos algorithm can only reach consensus on a single value. To achieve consensus on a series of values, we need to employ the Multi-Paxos concept.

⚠️**Note**: Multi-Paxos is merely a concept; the core of this concept is to reach consensus on a series of values through multiple Basic Paxos instances. In other words, Basic Paxos is at the heart of the Multi-Paxos concept, while Multi-Paxos simply entails executing Basic Paxos several times.

Since the Multi-Paxos concept mentioned by Lamport lacks the essential details for code implementation (such as how to elect a leader), it can be somewhat challenging to understand and implement.

However, there's no need to worry; we do not need to implement a consensus algorithm based on the Multi-Paxos concept ourselves. The industry has already produced some notable implementations. For instance, the Raft algorithm is a variant of Multi-Paxos that simplifies its ideas, making it easier to understand and implement. In actual projects, Raft should be prioritized.

## References

- <https://zh.wikipedia.org/wiki/Paxos>
- Consistency and Consensus Algorithms in Distributed Systems: <http://www.xuyasong.com/?p=1970>

<!-- @include: @article-footer.snippet.md -->
