---
title: Summary of Common Dubbo Issues
category: Distributed
tag:
  - rpc
---

::: tip

- Dubbo3 has been released, and this article is based on Dubbo2. Dubbo3 is evolved from Dubbo2, maintaining the original core functional characteristics while undergoing comprehensive upgrades in usability, large-scale microservice practices, cloud-native infrastructure adaptation, and security design.
- Many links in this article are outdated, mainly due to changes in the official Dubbo documentation that have caused URL failures.

:::

This article is a summary of Dubbo based on the official documentation and my own usage experience. Contributions are welcome!

## Dubbo Basics

### What is Dubbo?

![Dubbo Official Website](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/rpc/dubbo.org-overview.png)

[Apache Dubbo](https://github.com/apache/dubbo) |ˈdʌbəʊ| is a high-performance, lightweight open-source WEB and RPC framework.

According to the [official Dubbo documentation](https://dubbo.apache.org/zh/), Dubbo provides six core capabilities:

1. High-performance RPC calls oriented to interface proxies.
1. Intelligent fault tolerance and load balancing.
1. Automatic service registration and discovery.
1. Highly extensible capabilities.
1. Runtime traffic scheduling.
1. Visual service governance and operation.

![Six Core Capabilities Provided by Dubbo](https://oss.javaguide.cn/%E6%BA%90%E7%A0%81/dubbo/dubbo%E6%8F%90%E4%BE%9B%E7%9A%84%E5%85%AD%E5%A4%A7%E6%A0%B8%E5%BF%83%E8%83%BD%E5%8A%9B.png)

In simple terms: **Dubbo not only helps us call remote services but also provides other out-of-the-box features like intelligent load balancing.**

Dubbo currently has nearly 34.4k stars.

In the **2020 OSC China Open Source Project** selection event, Dubbo ranked 7th among development frameworks and basic component projects. Compared to a few years ago, its popularity and ranking have declined.

![](https://oss.javaguide.cn/%E6%BA%90%E7%A0%81/dubbo/image-20210107153159545.png)

Dubbo was open-sourced by Alibaba and later joined Apache. It is the emergence of Dubbo that has led to more and more companies beginning to use and accept distributed architectures.

### Why Use Dubbo?

With the development of the internet, websites are becoming larger, and the number of users is increasing. Single application architectures and vertical application architectures can no longer meet our needs, which is when distributed service architectures were born.

In a distributed service architecture, the system is split into different services, such as SMS service and security service, with each service independently providing a core service of the system.

We can use frameworks that support remote calls, such as Java RMI (Java Remote Method Invocation) and Hessian, to simply expose and reference remote services. However! As the number of services increases, the relationships between service calls become increasingly complex. When application access pressure increases, the need for load balancing and service monitoring becomes urgent. We can use hardware like F5 for load balancing, but this increases costs and poses a risk of single points of failure.

However, the emergence of Dubbo has solved the above problems. **What problems does Dubbo help us solve?**

1. **Load Balancing**: When the same service is deployed on different machines, which machine's service should be called?
1. **Service Call Chain Generation**: As the system evolves, the number of services increases, and the dependencies between services become complex, making it difficult to determine which application should start before which. Even architects cannot fully describe the architectural relationships of applications. Dubbo can help us understand how services call each other.
1. **Service Access Pressure and Duration Statistics, Resource Scheduling, and Governance**: Real-time management of cluster capacity based on access pressure to improve cluster utilization.
1. ……

![Overview of Dubbo Capabilities](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/rpc/dubbo-features-overview.jpg)

Additionally, Dubbo can be applied not only in distributed systems but also in the currently popular microservice systems. However, since Spring Cloud is more widely used in microservices, I believe that when we mention Dubbo, it is mostly in the context of distributed systems.

**We just mentioned the concept of distribution, so let’s introduce what distribution is and why it is necessary.**

## Basics of Distribution

### What is Distribution?

Distribution, or SOA, is fundamentally service-oriented. In simple terms, distribution means splitting the entire system into different services and placing these services on different servers to reduce the pressure on monolithic services and
