I don't...
title: Load balance and algorithms
Category: High performance
Head:

- Meta.
- Name: keywords
  Client: Client load balance, service load balance, Nginx, load balance algorithm, seven layer load balance, DNS resolution
- Meta.
- Name: description
  Content: Load balance refers to the distribution of user requests to different servers to improve the system's overall co-processing capacity. The load balance can be simply divided into the service-end load balance and the client load balance. The service-end load balance involves more knowledge points and more experience at work, as I will spend more time introducing.
  I don't...

What's the load balance?

**Load balance** refers to the distribution of user requests on different servers to improve the system's overall co-processing capacity and reliability. Load balance services can be performed by specialized software or hardware, which generally perform better, and the software is cheaper (detailed later).

The figure below is [Java Interview Point North](https://mp.weixin.qq.com/s?__bis=Mzg2OTA0Njk0OA=&mid=2247519384&idx=1&sn=bc7e71af753550b755f04ca41785b1a&chksm=cea1c335f9d64A458f7996d44d4d6e58637371a4612b8e4d106d83a6689e2b2cd7431&token=660789642&lang=zh_CN&sene=21#wechat_redact)'s chart showing that many of the system's commodity services were deployed on different servers and that we used a negative balance in order to achieve the demand for access to services.

![Multi-service example-load balance](https://oss.javaguide.cn/github/javaguide/high-perform/load-balancing/multi-service-load-balancing.drawio.png)

Load balance is a more commonly used and implemented means of improving the cooperation and reliability of systems, whether uni-structured or micro-serviced.

What are the load balances?

The load balance can be simply divided into **service-end load balance** and **client-end load balance**.

The service load balance involves more knowledge points and more experience at work, so I will spend more time introducing.

## Service load balance

**Service-end load balance** is mainly applied between **system external requests** and **gateway level**, and can be achieved using **software** or **hardware**.

The following is a simple, Nginx-based service load balance map I have drawn:

![A service-end load balance based on Nginx](https://oss.javaguide.cn/github/javaguide/high-perform/load-balancing/server-load-balancing.png)

**Hardware load balance** is achieved through specialized hardware equipment (e.g. **F5, A10, Array**).

Hardware load balance has the advantage of being highly performant and stable, and the disadvantage is that it is too expensive. Like basic F5, at least 200,000, most companies can't afford it.

In our day-to-day development, it is generally difficult to access the hardware load balance, and more often **the software load balance**. The software load balance achieves a load equilibrium function through software (e.g. **LVS, Nginx, HAproxy**) which is less effective but cheap! Like the basic Linux server, it's a few thousand, and a better performance, two to three million, is good.

According to the OSI model, the service-end load balance can also be divided into:

- Two-layer load balance.
- Three-layer load balance.
- Four-layer load balance.
- Seven-layer load balance.

The most common are the four- and seven-layer load balances, which are therefore also highlighted here.

The Nginx Network provides a detailed presentation of the four- and seven-layer load balance, which is interesting to see.

> - [What Is Layer 4 Load Balancing?](https://www.nginx.com/resources/glossary/layer-4-load-ballancing/)
> - [What Is Layer 7 Load Balancing?](https://www.nginx.com/resources/glossary/layer-7-load-ballancing/)

![OSI 7-Standard Model](https://oss.javaguide.cn/github/javaguide/cs-basics/network/osi-7-model.png)

- **Four Layer Load Balance** works on the 4th Level of the OSI Model, i.e. Transport Layer, where the main protocol is TCP/UDP. The load balancer can see the source port address in the data packet and the destination port address, and on the basis of this information forwards the data packet to the back-end real server by a certain load balance algorithm. In other words, the core of the four-layer load balance is the load balance at the IP+ port level, which does not involve specific content.
- **Seven Layer Load Balance** works on the 7th Level of the OSI Model, i.e. the application layer,
