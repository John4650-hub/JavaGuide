I don't...
Title: RPC Basics Summary\
Category: Distribution\
Tag:

- Rpc.\
  I don't...

This article briefly describes the underlying concept of RPC.

What is RPC?

**RPC (Remote Procedure Call)** is a remote process call, and by name, we can see that RPC is concerned with remote calls rather than local calls.

**Why RPC?** Because the method of service provision on two different servers is not in one memory space, it is necessary to transmit the parameters required by the method through a web program. Moreover, the results of a method call need to be received through a web program. However, the workload would be very large if we were to do this call process on our own and manually program it, because we need to consider the bottom-up mode of transmission (TCP or UDP), serialization, etc.

What can RPC do for us? **In short, RPC can help us access a service on a remote computer, a process that is as simple as a local one. And! We don't need to know the details of the underlying web programming.**

For example: Two different services A and B are deployed on two different machines, and Service A can call one of the B options through RPC.

One thing: **RPC is here to make it as simple as it is to call a remote method.**

# What's RPC's philosophy?

To help small partners understand the RPC principles, we can see the core functions of the entire RPC as the following five components:

1. **Client (service consumption end)**: one end of the remote approach.
1. **Client Stub (proxy)**: This is actually an agent category. The main thing that is done in the proxy category is simple, that is, to pass information on your call methods, classes, and method parameters to the service end.
1. **Network transmission**: Network transmission is the information that you want to transfer to the service end, e.g., parameters, and then, after the service implementation, transmits back to you via the network. There are a number of ways in which network transmission can be achieved, such as the most basic socket or the more advanced Netty (recommended).
1. **Stub**: This is not the proxy category. I don't think it's a good idea to understand it this way. The service end here, Stub, actually refers to the category where the result is returned to the client after receiving a request from the client to execute the method.
1. **Service end (service provider)**: one end of the provision of remote methods.

Here's the specifics, and I'll tell you about the entire RPC process.

[RPC rationale](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/37345851.jpg)

1. Service consumer (client) calls remote services on a local basis;
1. Client Stub (client stub) receives calls and is responsible for the assembly of methods, parameters, etc. into a message (sequencing) capable of network transmission: `RpcRequest`;
1. Client Stub (client stub) locates remote service addresses and sends messages to the service provider;
1. The service's Stub received information to re-serialize the message to Java: `RpcRequest`;
1. The service-end Stub calls local methods based on the type, method, methodological parameters, etc. in `RpcRequest`;
1. The service-end Stub obtained the results of the method implementation and assembled it as info capable of network transmission: `RpcResponse` (serialization) sent to the consumer;
1. The client Stub (client stub) received the message and converted it back-sequenced into a Java object: `RpcResponse`, thus obtaining the final result. Oh, over!

It is believed that the young partners have learned the rationale of RPC after reading the above-mentioned presentations.

It's not much, but it basically makes the core of the RPC framework clear! Also, for the technical details above, I will present them in the following section.

**Finally, with regard to RPC's rationale, it is hoped that the small partners will not only understand but will be able to draw it themselves and tell others. Because this problem occurs in interviews when the interviewer asks about the RPC content.**

# What are the common RPC frameworks?

What we're talking about here is the RPC framework that allows clients to call the service-end approach directly, just as simply as the local approach, like Dubbo, Motan, gRPC, which I'm talking about below. If you need to deal with the HTTP protocol, interpret and encapsulate HTTP requests and responses, this kind of framework is not an RPC framework, like Feign.

# Dubbo

![Dubbo](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/image202071161053081.png)

Apache Dubbo is a microservices framework that provides high-performance RPC solutions for large-scale microservices practices such as communication, traffic management, and observability.\
Covers multiple languages, such as Java, Golang, SDK
