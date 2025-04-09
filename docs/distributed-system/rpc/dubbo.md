I don't...
Title: Dubbo Summary of Common Issues
Category: Distribution
Tag:

- Rpc.
  I don't...

::: Tip

- Dubbo3 has been published, and the article is based on Dubbo2. Dubbo3 is based on the evolution of Dubbo2, while maintaining its original core functional characteristics. Dubbo3 has been fully upgraded in a number of broad directions, including ease of use, super-massive micro-service practices, adaptation to cloud-based infrastructure, and safety design.
- Many of the links in this document are no longer valid, mainly because of changes made to the Dubbo official document that invalidated the URL.

:::

This article is a summary of Dubbo, based on the official documents and their use in general. Welcome!

# Dubbo Base

What is Dubbo?

![Dubbo Network](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/rpc/dubbo.org-overview.png)

[Apache Dubbo](https://github.com/apache/dubbo) |ˈdʌbəʊ| is a high-performance, lightweight open-source WEB and RPC framework.

According to the [Dubbo official document](https://dubbo.apache.org/zh/), Dubbo provides six core competencies:

1. High-performance RPC calls for interface agents.
1. Smart tolerance and load balancing.
1. Automatic registration and discovery of services.
1. High scalability.
1. Movement control during the running period.
1. Visualized service governance and business.

![Six core competencies provided by Dubbo](https://oss.javaguide.cn/%E6%BA%90%E7%A081/dubbo/dubbo%E6%E6%E90%E4%BE%9B%E7%25E%9A%84%E5%E5%A5%A0%A4%A4%A6%A0%A0%E5%E5%E5%E8%E8%E8%E8%BD%E5%E5%25A%9B.png)

Simply put, **Dubbo not only helps us to call remote services but also provides some other open-in functionality, such as smart load balancing.**

Dubbo is now close to the star of 34.4k.

Dubbo ranked number 7 in the development framework and basic component type project for the **2020 China Open Source Project** selection campaign. Compared to a few years ago, the heat and ranking have decreased.

![Image](https://oss.javaguide.cn/%E6%BA%90%E7%A0%81/dubbo/image-20210107153159545.png)

Dubbo was an Ali open source project and later joined Apache. It is the emergence of Dubbo that has led to a growing number of companies starting to use and accept distributed structures.

Why Dubbo?

With the development of the Internet, the site is growing in size and number of users. A single application structure, a vertical application structure, was unable to meet our needs, and at that time distributed service structures were born.

Under the distributed service structure, the system is divided into different services, such as SMS, security services, and a core service provided by each service on its own.

We can use Java RMI (Java Remote Method Invocation) and Hessian, which support remote calls, to simply expose and quote remote services. But! As more and more services become available, the transfer relationship becomes more and more complex. The need for load balancing and service monitoring is also imminent when the pressure for application access increases. We can balance loads with F5, but this increases costs and risks single-point failure.

However, Dubbo's appearance has led to the resolution of these problems. **Did Dubbo help us solve any problems?**

1. **Load balancing**: which machine should be called when the same service is deployed on different machines.
1. **Service call link generation**: As the system develops, the number of services grows, the dependency relationships between services become complex, and it is not even clear which application should be started before which application is launched. The architects are unable to provide a complete description of the application architecture relationships. Dubbo can help us figure out how services are transferred between us.
1. **The service access pressure and the length of time, resource movement, and governance**: real-time cluster capacity management based on access pressure to increase cluster utilization.
1. ...

![Dubbo Capacity Overview](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/rpc/dubbo-features-overview.jpg)

In addition, Dubbo can be applied in the now-fired micro-services system in addition to being applied in distributed systems. However, because Spring Cloud is more widely used in micro-services, I think that most of Dubbo's remarks are in the case of distributed systems.

**We have just mentioned the concept of distribution. What is distribution? Why distribution?**

Distribution base

What's a distribution?

What's important about distribution, or SOA distribution, is service-oriented, or
