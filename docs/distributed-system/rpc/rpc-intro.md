---
title: Summary of Basic RPC Knowledge
category: Distributed
tag:
  - rpc
---

This article will briefly introduce the basic concepts related to RPC.

## What is RPC?

**RPC (Remote Procedure Call)** refers to remote procedure calls, and from its name, we can see that RPC focuses on remote calls rather than local calls.

**Why use RPC?** Because the methods provided by services on two different servers do not share the same memory space, it is necessary to use network programming to pass the parameters required for the method call. Furthermore, the results of the method call also need to be received through network programming. However, if we were to manually implement this calling process through network programming, the workload would be extensive, as we need to consider various factors such as the underlying transmission method (TCP or UDP), serialization methods, and more.

**What can RPC help us with?** Simply put, RPC allows us to call a method from a service on a remote computer as easily as calling a local method. Moreover, we do not need to understand the specific details of underlying network programming.

For example, if service A, deployed on one machine, wants to call a method from service B, deployed on another machine, it can do so via RPC.

In short: **The purpose of RPC is to make calling remote methods as simple as calling local methods.**

## What is the principle of RPC?

To help understand the principle of RPC, we can view the entire core functionality of RPC as being implemented in the following five parts:

1. **Client (Service Consumer)**: The end that calls the remote method.
1. **Client Stub**: This is essentially a proxy class. Its main responsibility is to pass information about the method call, class, and method parameters to the server.
1. **Network Transmission**: This involves sending the information regarding the method call, such as parameters, to the server. After the server completes execution, it sends the return result back to you via network transmission. There are many ways to implement network transmission; for example, the basic Socket or the more efficient and encapsulated Netty (recommended).
1. **Server Stub**: This stub is not a proxy class. It's important to note here that the server stub refers to the class that receives the method execution request from the client, executes the corresponding method, and then returns the result to the client.
1. **Server (Service Provider)**: The end that provides the remote method.

The detailed principle diagram is as follows, and later I will explain the entire RPC process to everyone.

![RPC Principle Diagram](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/37345851.jpg)

1. The service consumer (client) calls the remote service as if it were a local call;
1. The client stub receives the call and is responsible for assembling the method, parameters, etc., into a message body for network transmission (serialization): `RpcRequest`;
1. The client stub finds the address of the remote service and sends the message to the service provider;
1. The server stub receives the message and deserializes it into a Java object: `RpcRequest`;
1. The server stub invokes the local method based on the class, method, and method parameters in the `RpcRequest`;
1. The server stub obtains the execution result and assembles it into a message body for network transmission: `RpcResponse` (serialization) to send back to the consumer;
1. The client stub receives the message and deserializes it into a Java object: `RpcResponse`, thus obtaining the final result. Over!

I believe that after reading the explanation above, everyone understands the principle of RPC.

Although the content is not extensive, it effectively clarifies the core principles of RPC frameworks! Additionally, I will introduce the technical details mentioned above in subsequent sections.

**Finally, when it comes to the principles of RPC, I hope everyone not only understands but can also reproduce it and explain it to others. This is a common question you will encounter in interviews when the interviewer asks about RPC-related content.**

## What are some common RPC frameworks?

The RPC frameworks we refer to here are those that allow clients to directly call server methods as easily as calling local methods, such as Dubbo, Motan, and gRPC that I will introduce below. If you need to interact with the HTTP protocol, parse, and encapsulate HTTP requests and responses, those frameworks cannot be classified as "RPC frameworks," such as Feign.

### Dubbo

![](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/image-20220716111053081.png)

Apache Dubbo is a microservice framework that provides high-performance RPC communication, traffic governance, observability, and other solutions for large-scale microservices practices, covering various language SDK implementations including Java and Golang.

Dubbo offers almost all service governance capabilities, from service definition, service discovery, service communication to traffic control, supporting the Triple protocol (the next generation RPC communication protocol defined over HTTP/2), application-level service discovery, and Dubbo Mesh (Dubbo3 has introduced many cloud-native friendly new features).

![](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/image-20220716111545343.png)

Dubbo was open-sourced by Alibaba and later joined Apache. It is due to the emergence of Dubbo that more and more companies have begun to adopt and accept distributed architectures.

Dubbo is considered one of the more excellent domestic open-source projects, and its source code is worthy of study and reading!

- GitHub: [https://github.com/apache/incubator-dubbo](https://github.com/apache/incubator-dubbo "https://github.com/apache/incubator-dubbo")
- Official Website: <https://dubbo.apache.org/en/>

### Motan

Motan is an RPC framework open-sourced by Sina Weibo, reportedly supporting billions of calls at Sina Weibo. However, the author has rarely seen any companies using it, and there are also relatively few resources available online.

Many people like to compare Motan with Dubbo, as both are open-sourced by large domestic companies. After reviewing many materials and briefly checking the source code, I found that **Motan is more like a simplified version of Dubbo. It may have borrowed ideas from Dubbo, but its design is simpler and its functionality more refined.**

However, I do not recommend using Motan in actual projects. If your company intends to use it, I still recommend Dubbo, as its community activity and ecosystem are much better.

- Looking at RPC framework design from Motan: [http://kriszhang.com/motan-rpc-impl/](http://kriszhang.com/motan-rpc-impl/ "http://kriszhang.com/motan-rpc-impl/")
- Motan Chinese Documentation: [https://github.com/weibocom/motan/wiki/zh_overview](https://github.com/weibocom/motan/wiki/zh_overview "https://github.com/weibocom/motan/wiki/zh_overview")

### gRPC

![](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/2843b10d-0c2f-4b7e-9c3e-ea4466792a8b.png)

gRPC is a high-performance, open-source RPC framework developed by Google. It is primarily designed for mobile application development and is based on the HTTP/2 protocol standard (supporting bidirectional streaming, message header compression, etc., which saves bandwidth), and is developed based on the ProtoBuf serialization protocol, supporting many programming languages.

**What is ProtoBuf?** [ProtoBuf (Protocol Buffer)](https://github.com/protocolbuffers/protobuf) is a more flexible and efficient data format that can be used in communication protocols, data storage, and other fields. It supports almost all mainstream programming languages and is platform-independent. However, defining interfaces and data types using ProtoBuf can be a bit cumbersome; that's a minor drawback.

![](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/image-20220716104304033.png)

It must be said that the design of gRPC's communication layer is exceptionally well done, and the communication layer improvement of [Dubbo-go 3.0](https://dubbogo.github.io/) has heavily borrowed from gRPC.

However, gRPC's design leads to almost no service governance capabilities. If you want to solve this problem, you will need to rely on other components, such as Tencent's PolarisMesh (North Star).

- GitHub: [https://github.com/grpc/grpc](https://github.com/grpc/grpc "https://github.com/grpc/grpc")
- Official Website: [https://grpc.io/](https://grpc.io/ "https://grpc.io/")

### Thrift

Apache Thrift is a cross-language RPC communication framework open-sourced by Facebook and is now managed by the Apache Foundation. Due to its cross-language features and excellent performance, it is applied in many internet companies. Capable companies may even develop a distributed service framework based on Thrift, adding features such as service registration and service discovery.

`Thrift` supports various **programming languages**, including `C++`, `Java`, `Python`, `PHP`, `Ruby`, etc., and it supports more languages compared to gRPC.

- Official Website: [https://thrift.apache.org/](https://thrift.apache.org/ "https://thrift.apache.org/")
- Simple Introduction to Thrift: [https://www.jianshu.com/p/8f25d057a5a9](https://www.jianshu.com/p/8f25d057a5a9 "https://www.jianshu.com/p/8f25d057a5a9")

### Conclusion

Although gRPC and Thrift support cross-language RPC calls, they only provide the most basic RPC framework functionalities, lacking a series of supporting services and governance features.

Dubbo is the most outstanding framework in terms of functionality completeness, ecosystem, and community activity. Moreover, there are many successful domestic cases of Dubbo, such as Dangdang, Didi, and others; it is a mature and stable RPC framework that has withstood practical tests. Most importantly, you can find a wealth of reference materials on Dubbo, making the learning curve relatively low.

The following diagram shows Dubbo's ecosystem.

![](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/eee98ff2-8e06-4628-a42b-d30ffcd2831e.png)

Dubbo is also a component of Spring Cloud Alibaba.

![](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/0d195dae-72bc-4956-8451-3eaf6dd11cbd.png)

However, both Dubbo and Motan are mainly used for the Java language. Although they can currently also support some other languages, it's not highly recommended. If you need to call across multiple languages, consider using gRPC.

In summary, if you are in a Java backend tech stack and are conflicted about which RPC framework to choose, I recommend considering Dubbo.

## How to design and implement an RPC framework?

**"Writing an RPC Framework"** is an internal booklet of my [Knowledge Planet](https://javaguide.cn/about-the-author/zhishixingqiu-two-years.html), where I wrote 12 articles explaining how to start from scratch and implement a simple RPC framework based on Netty+Kyro+Zookeeper.

Although the project is compact, it covers all essential aspects, with detailed comments in the code, a clear structure, and integrated Check Style norms for code structure, making it very suitable for reading and learning.

**Content Overview**:

![](https://oss.javaguide.cn/github/javaguide/image-20220308100605485.png)

## Since there is an HTTP protocol, why is there still a need for RPC?

For a detailed answer to this question, please refer to this article: [Since there is an HTTP protocol, why is there still a need for RPC?](http&rpc.md).
