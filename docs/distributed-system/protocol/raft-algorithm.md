I don't...
title: Raft
Category: Distribution
Tag:

- Distributive protocols & algorithms
- Consensus algorithm.
  I don't...

> This paper was jointly prepared by [SnailClimb](https://github.com/Snailclimb) and [Xieqijun](https://github.com/jun0315).

# 1 Background

Today's data centers and applications operate in a highly dynamic environment and, in response to this environment, they expand horizontally through additional servers and expand and contract according to demand. At the same time, server and network failures are common.

As a result, the system has to process the addition and removal of servers during normal operations. They must react to changes and adapt automatically within a few seconds; for clients, a clear interruption is usually unacceptable.

Fortunately, distributed consensus can help to address these challenges.

## General Byzantine

Before introducing the consensus algorithm, an example of a simplified version of General Byzantine would help to understand the consensus algorithm.

> How can the generals reach a unanimous decision on whether to attack, assuming that many Byzantines do not have rebels, and that the couriers have reliable information but are likely to be assassinated?

The solution is broadly understood as the selection of a general by all generals to make all decisions.

For example, if there are now three generals A, B, and C, each general has a random time countdown, and as soon as the countdown is over, the general sees himself as a candidate for the general and sends a messenger to pass on the election to Generals B and C. If Generals B and C have not considered themselves candidates (whose countdown is not over) and have not voted for others, they will vote for General A. When the messenger returns to General A, General A knows that they have received enough votes to become the general. After the arrival of the Great General, it is up to General A to decide whether or not to attack and then to send a messenger to inform the other two Generals that he has become the Great General himself. If a reply from Generals B and C has not been received for some time (the courier may have been assassinated), send another messenger until a reply is received.

## 1.2 Consensus Algorithm

Consensus is a fundamental issue in the system of tolerance: even in the face of failure, servers could agree on a shared status.

The consensus algorithm allows a group of nodes to work together as a whole, even if some of them fail, and can continue to work, the correctness of which stems mainly from the nature of the copying-state machine: a group of `Server` status machines calculate copies of the same state, even if some of the `Server` machines are defunct and can continue to operate.

![rsm-architecture.png](rsm-architecture.png)

`Chart-1 Copy Status Structure`

Reproduction status machines are generally achieved by using a copy log. Each `Server` stores a log file containing a sequence of orders, with an opportunity to execute those orders sequentially. Because each log contains the same command and the same sequence, each status machine processes the same command sequence. Since the status machine is certain, it handles the same state and receives the same output.

Consensus algorithms are therefore designed to maintain consistency in the reproduction of logs. The consensus module on the server receives commands from the client and adds them to the log. It communicates with consensus modules on other servers to ensure that even if some servers fail, each journal ultimately contains requests in the same order. Once the orders have been correctly reproduced, they are referred to as having been submitted. Each server's status machine processes submitted commands in log order and returns the output to the client, thus creating a single, highly reliable status machine.

Consensus algorithms applicable to the actual system usually have the following characteristics:

- Clear. To ensure security under the non-Byzantine conditions (i.e., the simplified Byzantine version mentioned above), including network delays, zoning, loss of packets, copying, and reordering.
- High availability. As long as most servers are operational and can communicate with each other or with clients, they can be considered fully functional. Thus, a typical cluster of five servers could tolerate any failure of two servers. It is assumed that servers will fail as a result of the stoppage; they may later recover from stable storage and re-enter the cluster.
- Coherence does not depend on time series. Delays in the wrong clock and extreme news also create problems of usability in the worst-case scenario, not consistency.
- Most of the servers in the cluster respond, so that the command can be completed without being influenced by a small number of slow-moving servers.

# 2 Base

## 2.1 Node Type

A Raft cluster includes a number of servers, using a typical 5-server cluster as an example. At any given time, each server must be in one of the following three states:

- `Leader`: Responsible for launching heartbeats, responding to clients, creating logs, synchronizing logs.
- `Candidate`:
