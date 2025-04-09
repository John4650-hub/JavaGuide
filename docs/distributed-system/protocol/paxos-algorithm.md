I don't...
type: Paxos algorithm
Category: Distribution
Tag:

- Distributive protocols & algorithms
- Consensus algorithm.
  I don't...

# Background

The Paxos algorithm was presented by Leslie Lamport (https://zh.wikipedia.org/wiki/Lesley_Lamport) in 1990 as a distributed system consensus algorithm. It is also the first consensus algorithm to prove to be complete (provided there is no question of General Byzantine, i.e., no malice).

For the presentation of the Paxos algorithm, Lamport wrote a funny paper. In this paper, he virtualized a Greek city-state called Paxos to present Paxos algorithms more graphically.

However, the reviewer did not accept the humor of the paper. So they said to Lamport, "If you want to make this paper a success, you have to delete all the stories about Paxos." Lamport was not happy at all: “How can I modify this? You readers lack a sense of humor; you can't send it!”

So, the paper that presented Paxos algorithms was not published successfully at that time.

Until 1998, two technical researchers at the Centre for Systems Research (SRC) needed to find suitable distributed algorithms to service the distributed systems they were building, and the Paxos algorithms were able to address some of their needs. So Lamport sent them the paper. After reading the paper, the two bosses thought it was a good one. As a result, Lamport republished his paper in 1998 [The Part-Time Parliament](http://lamport.azurewebsites.net/pubs/lamport-paxos.pdf).

Following the presentation of the paper, various scholars spoke out and did not understand it. In 2001, Lamport wrote another paper to simplify the presentation of Paxos, focusing on the two-stage consensus section, without forgetting to mock the group of scholars.

The paper “Paxos Made Simple” is 14 pages, much shorter than the 33 pages of The Part-Time Parliament. The most important thing is that the summary of the paper says:

![./images/paxos/paxos-made-simple.png]

The Paxos algorithm, when prepared in plain English, is very simple.

The translation means that when I describe it in uncomplicated English, the Paxos algorithm is really simple!

Do you feel the smell of sarcasm from Big Lamport?

# Introduction

The Paxos algorithm is the first proven distributed system consensus algorithm. Consensus algorithms serve to reach agreement on a proposal (Proposal) between the multiple nodes of the distributed system. The meaning of the proposal is very broad in a distributed system, and any node such as the Leader node, the sequence of multiple events could be a proposal.

The Paxos algorithm that Lamport proposed at the time consisted mainly of two parts:

- **Basic Paxos algorithm**: describes how a consensus on a value (proposal value) is reached between multiple nodes.
- **Multi-Paxos Ideology**: Description of several examples of the implementation of Basic Paxos to reach consensus on a range of values. Multi-Paxos says it's the execution of Basic Paxos many times, and the core is Basic Paxos.

Attempts have been made to simplify the Paxos algorithm because it is very difficult to understand and realize it internationally. It was only in 2013 that a consensus algorithm - [Raft] - was created that is easier to understand and realize than the Paxos algorithm (https://javaguide.cn/distributed-system/theorem&algorithem&protocol/raft-algorithm.html). More specifically, Raft is a variant of Multi-Paxos, which simplifies Multi-Paxos' thinking, becomes easier to understand, and works to achieve.

In the case of no malicious nodes, except for the Raft algorithm, some of the most commonly used consensus algorithms at present, such as **ZAB protocol** and **Fast Paxos** algorithms, are based on the Paxos algorithm.

In cases of malicious nodes, consensus algorithms such as **Workload Certificate (PoW, Proof-of-Work)** and **Certificate of Interests (PoS, Proof-of-Stake)** are generally used. The most typical application of this type of consensus algorithm is the blockchain, for example, where the previous period had been officially declared by the Taifeng to have its consensus mechanism transformed from a Workload Certificate (PoW) to a Proof of Interests (PoS).

The central problem with the consensus algorithms used by the blockchain system is the question of General Byzantine, which is different from the distributed intermediates of ZooKeeper, Etcd, and Consul that we are exposed to on a daily basis.

Let's summarize the definition of the Paxos algorithm:

- The Paxos Algorithm was Lamport's introduction in 1990 of a distributed system consensus algorithm.
- The Paxos algorithm that Lamport then proposed consisted mainly of two parts: the Basic Paxos algorithm and the Multi-Paxos idea.
- Raft algorithms, ZAB protocols, and Fast Paxos algorithms are
