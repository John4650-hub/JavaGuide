I don't...
title: CAP & BASE theoretical details
Category: Distribution
Tag:

- Distributive theory
  I don't...

The two theories of CAP & BASE have become more familiar to the young people who have undergone technical interviews!

When I was interviewed that year, I said, without exaggerating, that as long as I was asked about the distributional content, the interviewer would almost certainly ask about the two distributional theories. One is that these two distributive fundamental theories are the necessary pre-element for learning distributed knowledge, and the other is that many interviewers themselves are more familiar with the two theories (facilitating questions).

It is very important that we understand these two theories and can speak to others with our own understanding.

# CAP Theory

[CAP theory/theory](https://zh.wikipedia.org/wiki/CAP%E5%AE%9A%E7%E7%90%86) originated in 2000 and was presented by Eric Brewer, Professor, University of California, Berkeley, at the distributive computational principles seminar (PODC) and therefore CAP theory is called **Brewer's theory**.

Two years later, Seth Gilbert and Nancy Lynch of the Massachusetts Institute of Technology provided evidence of Brewer's conjecture, and the CAP theory was officially recognized as a theorem in the distributed field.

# Brief

**CAP**, i.e. **Consistency**, **Availability**, **Partition tolerance**

(https://oss.javaguide.cn-2020-11/cap.png)

When Brewer, the proponent of the CAP theory, raised the CAP conjecture, there was no clear definition of the three terms **Consistency**, **Availability**, **Partition Tolerance**.

So there's a lot of civil interpretation of the CAP, and it's generally recommended that the following version of this interpretation.

In theoretical computer science, the CAP theorem states that for a distributed system, only two of the following three points can be satisfied when a reading and writing operation is designed:

- **Consistency**: all nodes access the same latest copy of data
- **Available (Availability)**: non-failed nodes return a reasonable response within a reasonable time (not an error or time-out response).
- **Partition tolerance**: The distributed system can still provide services when network partitions occur.

**What's a network partition?**

In the distributed system, the network of multiple nodes would have been connected, but because of certain faults (such as some nodes having problems with the network) were not connected to some nodes, the entire network was divided into several areas, which is called a **network partition**.

[Partition-tolerance](https://oss.javaguide.cn-2020-11/Partition-tolerance.png)

# It's not called '3 Choose 2'

Most interpretations of this law often simply state: “consistency, availability, partition tolerance can only be achieved in two of them at the same time and cannot be achieved at the same time.” In fact, this is a very misleading statement, and 12 years after the CAP theory was born, the CAP's father rewrote the previous paper in 2012.

> **When a network partition occurs, if we continue to serve, then only 2 options for greater consistency and availability are available. This means that after the network partition P is a prerequisite, after P has decided to choose C and A. That means that we have to achieve the faultiness of the partition.**
>
> In short: The partition tolerance P in CAP theory must be satisfied, on the basis of which only availability A or consistency C can be satisfied.

Therefore, it is theoretically impossible for the **distributed system to choose CA architecture, only CP or AP architecture.** For example, ZooKeeper and HBase are the CP architecture, Cassandra and Eureka are the AP architecture, and Nacos supports not only the CP architecture but also the AP architecture.

**Why can't we choose the CA structure?** For example: if a "partition" occurs in the system, a certain node in the system is written. In order to ensure C, the reading and writing of other nodes must be banned, which is in conflict with A. If the reading and writing of the other nodes are normal, it's a conflict with C.

**The key to choosing CP or AP is the current business landscape, which is not conclusive, for example, for situations where there is a need to ensure strong consistency, as in the case of banks.**

In addition, it needs to be added that **if the network partitions are normal (the system is in this state most of the time), it is not necessary to guarantee P when C and A can guarantee both.**

# CAP practical application case

I'm here to explore the practical application of CAP. Given that many small partners do not know what the registration center is for, let's just say Dubbo.

The chart below shows Dubbo's structure. What role does the Registry play in this? What services are provided? \*\*

The registration center is responsible for the registration and search of the service address, which is equivalent to a catalog
