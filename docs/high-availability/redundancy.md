---
title: Detailed Explanation of Redundant Design
category: High Availability
icon: cluster
---

Redundant design is the most common means to ensure high availability of systems and data.

For services, the idea of redundancy is to deploy multiple instances of the same service. If the currently used service suddenly fails, the system can quickly switch to a backup service, significantly reducing downtime and improving system availability.

For data, the idea of redundancy is to have multiple backups of the same data, which can easily enhance data security.

In fact, there are many applications of redundancy in daily life.

For example, my method of saving important files is an application of redundancy. I synchronize important files I use daily to both GitHub and my personal cloud storage, ensuring that even if my computer's hard drive fails, I can retrieve my important files from GitHub or my personal cloud.

High Availability Cluster (HA Cluster), local disaster recovery, remote disaster recovery, local active-active, and remote active-active are the most typical applications of redundancy in high availability system design.

- **High Availability Cluster**: The same service is deployed in two or more instances. If the currently used service suddenly fails, it can switch to another service, ensuring high availability.
- **Local Disaster Recovery**: An entire cluster can be deployed in the same data center, while in local disaster recovery, the same service is deployed in different data centers within the same city. Additionally, the backup service does not handle requests. This helps avoid unexpected situations in the data center, such as power outages or fires.
- **Remote Disaster Recovery**: Similar to local disaster recovery, but the same service is deployed in different data centers in remote locations (usually far apart, even in different cities or countries).
- **Local Active-Active**: Similar to local disaster recovery, but the backup service can handle requests, allowing for better utilization of system resources and increased concurrency.
- **Remote Active-Active**: Services are deployed in different data centers in remote locations, and they can provide services simultaneously.

High Availability Clusters focus solely on service redundancy without emphasizing geographic location. Local disaster recovery, remote disaster recovery, local active-active, and remote active-active achieve geographic redundancy.

The main difference between local and remote is the distance between data centers. Remote locations are usually farther apart, even in different cities or countries.

Compared to traditional disaster recovery design, the most significant change in local active-active and remote active-active is the "active-active" aspect, meaning all sites are simultaneously providing services. Remote active-active is designed to respond to emergencies such as fires, earthquakes, or other natural or man-made disasters.

Simply having redundancy is not enough; it must be paired with **failover**! Failover, in simple terms, is the ability to quickly and automatically switch from an unavailable service to an available one without human intervention.

For example, in a Redis cluster using Sentinel mode, if the Sentinel detects a failure in the master node, it will help us achieve failover by automatically promoting one of the slaves to master, ensuring the availability of the entire Redis system. The entire process is fully automated and requires no manual intervention. I have detailed the relevant knowledge and interview questions about Redis clusters in the database section of [“Java Interview Guide”](https://www.yuque.com/docs/share/f37fc804-bfe6-4b0d-b373-9c462188fec7), which interested readers can check out.

Another example: Nginx can be combined with Keepalived to achieve high availability. If the primary Nginx server goes down, Keepalived can automatically perform failover, promoting the backup Nginx server to primary. Moreover, this switch is transparent to external users because a virtual IP is used, which does not change. I have detailed the relevant knowledge and interview questions about Nginx in the "Servers" section of [“Java Interview Guide”](https://www.yuque.com/docs/share/f37fc804-bfe6-4b0d-b373-9c462188fec7), which interested readers can check out.

Implementing a remote active-active architecture is very challenging, with many factors to consider. I have not personally practiced remote active-active architecture in actual projects, and my understanding remains at the theoretical level.

If you want to delve deeper into knowledge related to remote active-active, I recommend a few articles that I find quite good:

- [Understanding Remote Active-Active, This Article is Enough - Water Droplets and Silver Bullets - 2021](https://mp.weixin.qq.com/s/T6mMDdtTfBuIiEowCpqu6Q)
- [Building Remote Active-Active in Four Steps](https://mp.weixin.qq.com/s/hMD-IS__4JE5_nQhYPYSTg)
- [“Learning Architecture from Scratch” — 28 | Ensuring Business High Availability: Remote Active-Active Architecture](http://gk.link/a/10pKZ)

However, most of these articles mainly introduce conceptual knowledge. Currently, there is
