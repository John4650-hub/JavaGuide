---
title: With HTTP Protocol, Why is RPC Still Needed?
category: Distributed
tag:
  - rpc
---

> This article is contributed by [Xiao Bai debug](https://juejin.cn/user/4001878057422087), original text: <https://juejin.cn/post/7121882245605883934>.

I remember when I first started working and encountered RPC protocol for the first time. I was confused; I was using HTTP protocol just fine, so why do we need RPC protocol?

So, I searched online.

Many explanations seemed very official. I'm sure everyone has seen them on various platforms, explaining but seemingly not explaining; they all used **one concept we don't understand to explain another concept we don't understand**. Those who understood didn't need to read it, and those who didn't understand remained confused.

That feeling of having read but not having grasped it is quite uncomfortable, **I understand**.

To avoid eliciting strong **aesthetic fatigue**, let's try to explain this in a different way today.

## Let's Start with TCP

As a programmer, suppose we need to send some data from a process on computer A to a process on computer B, we typically use sockets in our code for programming.

At this point, our options generally come down to **TCP or UDP**. TCP is reliable, while UDP is not. Unless someone like Ma Huateng, a legendary programmer (who heavily used UDP in early QQ), opts for UDP, most ordinary people tend to choose TCP without thinking if there's any requirement for reliability.

It looks something like this.

```ini
fd = socket(AF_INET,SOCK_STREAM,0);
```

Where `SOCK_STREAM` indicates the use of **byte stream** to transmit data, which simply means **TCP protocol**.

After defining the socket, we can happily perform operations on it, such as using `bind()` to bind the IP port and `connect()` to initiate a connection.

![Handshake Connection Process](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/f410977cda814d32b0eff3645c385a8a~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp.png)

Once the connection is established, we can use `send()` to send data and `recv()` to receive data.

A pure naked TCP connection can achieve data transmission; isn't that enough?

No, there are problems with using it this way.

## What are the Problems with Using Pure Naked TCP?

It's often repeated that TCP has three characteristics: **connection-oriented**, **reliable**, and based on **byte stream**.

![What is TCP](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/acb4508111cb47d8a3df6734d04818bc~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp.png)

These three characteristics are indeed **very concise**; we didn’t memorize them for nothing.

Each characteristic can be discussed in its own article, but today we need to focus on **byte stream**.

A byte stream can be understood as flowing binary data in a bidirectional channel, which is essentially a string of **01**. The 01 strings transmitted by pure naked TCP have **no boundaries** between them; you have no idea where a complete message starts and ends.

![Binary Byte Stream](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/b82d4fcdd0c4491e979856c93c1750d7~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp.png)

Because of this lack of boundaries, when we choose to send **"Xia Luo" and "Te Fan Nao"** over TCP, the receiving end gets **"Xia Luo Te Fan Nao."** At this point, the receiver cannot distinguish whether you meant to express **"Xia Luo" + "Te Fan Nao"** or **"Xia Luo Te" + "Fan Nao."**

![Message Comparison](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/4e120d0f1152419585565f693e744a3a~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp.png)

This is known as the **sticky packet problem**, and I have previously written a dedicated [article](https://mp.weixin.qq.com/s/0-YBxU1cSbDdzcZEZjmQYA) discussing this issue.

The point of mentioning this is to let everyone know that pure naked TCP cannot be used directly; you need to add some **custom rules** to distinguish **message boundaries.**

So, we typically wrap each piece of data to be sent, such as adding a **message header** that clearly states the length of a complete packet. Based on this length, we can continue receiving data and extract it, which forms our actual **message body**.

![Message Boundary Length Indicator](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/cb29659d4907446e9f70551c44c6369f~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp.png)

The mentioned **message header** can also contain various pieces of information, such as whether the message body is compressed and the format of the message body. As long as both ends agree on the protocol, it's fine.

Every project using TCP may define a set of parsing standards similar to this; they may **differ but the principles are similar.**

**Thus, many protocols have emerged based on TCP, such as HTTP and RPC.**

## HTTP and RPC

### RPC is Essentially a Calling Method

Let’s turn back to the network layering diagram.

![Four-Layer Network Protocol](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/04b603b5bd2443209233deea87816161~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp.png)

**TCP is a transport layer protocol**, while HTTP and various RPC protocols built on TCP are merely **application layer protocols** that define different message formats.

**HTTP** (Hyper Text Transfer Protocol) is commonly known as the **hypertext transfer protocol**. We use it quite often; when browsing the internet, we can access a webpage by typing a URL in the browser, which utilizes the HTTP protocol.

![HTTP Call](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/8f07a5d1c72a4c4fa811c6c3b5aadd3d~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp.png)

On the other hand, **RPC** (Remote Procedure Call) is also known as **remote procedure invocation**. It is not a specific protocol; it represents a **calling method**.

For example, when we usually call a **local method**, it looks something like this.

```ini
res = localFunc(req)
```

If now this isn't a local method, but a method `remoteFunc` exposed by a **remote server**, and we can still call it just like a local method, it can **mask some network details**, making it more convenient, wouldn't that be great?

```ini
res = remoteFunc(req)
```

![RPC Can Call Remote Methods Like Local Methods](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/761da6c30af244e19b1c44075d8b4254~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp.png)

Based on this idea, many different types of RPC protocols have been created, such as the well-known `gRPC` and `thrift`.

It's important to note that although most RPC protocols use TCP underneath, in reality, **they don't have to use TCP; they could also use UDP or HTTP, achieving similar functionalities.**

Now, let's return to the question posed in the article title.

### If RPC Exists, Why is HTTP Still Needed?

In fact, TCP was a protocol developed in the **1970s**, while HTTP didn't begin to gain popularity until the **1990s**. Given that directly using naked TCP has its problems, it’s easy to see how many custom protocols have emerged over the years; and RPC was developed in the **1980s**.

Therefore, we should not ask **why RPC exists alongside HTTP** but rather **why does HTTP still exist alongside RPC?**

Various networked applications installed on computers, such as xx Manager and xx Guardian, act as clients requiring connection to servers to send and receive messages; they utilize application layer protocols. In this Client/Server (C/S) architecture, they can use their own RPC protocols since they only need to connect to their company’s server.

However, one type of software is different: browsers (Browser). Whether it’s Chrome or IE, they need not only to access their own company’s **server** but also to reach websites hosted by other companies; therefore, they need a unified standard, otherwise, communication would be impossible. Consequently, HTTP serves as that standard for unifying **Browser/Server (B/S)** communication.

In other words, many years ago, **HTTP was primarily used for B/S architecture, while RPC was more for C/S architecture. However, such distinctions have blurred over time; B/S and C/S are gradually merging.** Many applications now support multiple platforms, such as a certain cloud storage service needing both **web** and **mobile/PC** support. If communication protocols only used HTTP, the server would only need to deal with one set of protocols. Consequently, RPC has begun to take a backseat and is generally used for communication between various microservices within company internal clusters.

So that raises the question: **Why not just use HTTP; why use RPC at all?**

It feels like we're back at the beginning of the article, so we’ll need to discuss the differences between them.

### What Are the Differences Between HTTP and RPC?

Let's look at a few points that clearly distinguish RPC from HTTP.

#### Service Discovery

To initiate a request to a server, you must first establish a connection, and the prerequisite for establishing that connection is knowing the **IP address and port**. The process of finding the service's corresponding IP and port is known as **service discovery**.

In **HTTP**, if you know the domain name of the service, you can resolve the IP address behind it through **DNS services**, typically on the **default port 80**.

In the case of **RPC**, there are some differences. Typically, there will be dedicated intermediary services responsible for storing service names and IP information, such as **Consul, Etcd, Nacos, ZooKeeper, or even Redis**. To access a certain service, you'd query these intermediary services to obtain the IP and port information. Since DNS also functions as a form of service discovery, there exist components that achieve service discovery based on DNS, such as **CoreDNS**.

It's evident that the service discovery aspect does show some differences between the two, but they are not significantly superior or inferior to one another.

#### Underlying Connection Method

Taking the mainstream **HTTP/1.1** protocol as an example, its default behavior is to maintain a TCP connection once established (the **keep-alive** feature), allowing subsequent requests and responses to reuse this connection.

On the other hand, **RPC protocols** also establish TCP long connections for data interactions, but unlike HTTP, RPC protocols typically create a **connection pool**. During times of high request volume, multiple connections are established and kept in the pool; when data needs to be sent, a connection is retrieved from the pool, used, and then returned for future reuse, which is quite efficient.

![Connection Pool](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/72fcad064c9e4103a11f1a2d579f79b2~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp.png)

Connection pools help improve network request performance, which is why many programming languages' network libraries introduce a connection pool for HTTP, as seen in Go.

From this perspective, there isn’t a significant distinction in practices between them, so it’s not a key difference either.

#### Content Being Transmitted

Messages transmitted using TCP ultimately consist of **header and body**.

**Header** is used to denote certain special information, with the most important aspect being the **length of the message body**.

**Body** contains the actual content we need to transmit, which must be represented in binary as 01 strings since computers only recognize this format. Thus, TCP can handle strings and numbers without issue, because strings can be encoded as 01 strings, and numbers can be directly converted into binary. For structures, however, we need a method to convert them into binary 01 strings, and there are many existing solutions for this, such as **JSON** and **Protocol Buffers (Protobuf)**.

The process of converting a structure into a binary array is called **serialization**, while reverting a binary array back into a structure is called **deserialization**.

![Serialization and Deserialization](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/d501dfc6f764430188ce61fda0f3e5d9~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp.png)

For mainstream HTTP/1.1, although it is referred to as a hypertext protocol—supporting audio and video—HTTP was originally designed primarily for displaying web text, so the content it transmits is predominantly string-based. Both the header and body follow this pattern. In the body section, it employs **JSON** to **serialize** structured data.

We can capture a snapshot for a more intuitive view.

![HTTP Message](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/04e8a79ddb7247759df23f1132c01655~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp.png)

The content here exhibits significant redundancy and appears verbose. Most noticeably, information in the header (like `Content-Type`) could be effectively conveyed by agreeing on a specific bit's position in the header instead of transmitting the `Content-Type` field literally each time. Similar situations can also be observed in the body's JSON structure.

In contrast, RPC, due to its higher level of customization, can utilize smaller serialization formats like Protobuf to retain structured data, without needing to accommodate various browser behaviors (like 302 redirection). **Thus, its performance can be better, which is a major reason for opting for RPC instead of HTTP in internal microservice communications.**

![HTTP Principle](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/284c26bb7f2848889d1d9b95cf49decb~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp.png)

![RPC Principle](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/edb050d383c644e895e505253f1c4d90~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp.png)

It’s worth noting that the HTTP referred to here specifically denotes the currently mainstream HTTP/1.1. `HTTP/2` has made many improvements on top of this, so **its performance may exceed many RPC protocols**, with `gRPC` directly utilizing `HTTP/2` at its core.

This prompts yet another question.

### Why, Since We've Emerged with HTTP/2, Do We Still Need RPC Protocols?

The reason is that HTTP/2 was introduced in 2015, by which time many internal RPC protocols had already been running for several years; thus, due to historical reasons, there typically wasn’t a pressing need to shift to alternatives.

## Conclusion

- Pure naked TCP can send and receive data, but it is a borderless data stream, requiring the upper layers to define a message format to establish **message boundaries**. Thus, various application layer protocols, including HTTP and various RPC protocols, are defined on top of TCP.
- **RPC is not essentially a protocol but a method of invocation**, while implementations like gRPC and Thrift are the protocols that realize RPC calls. The goal is to allow programmers to invoke remote service methods like local methods. Moreover, there are various ways to implement RPC, **and it does not have to be based on the TCP protocol**.
- From a historical development perspective, **HTTP was primarily used for B/S architecture, while RPC was more oriented towards C/S architecture. However, now the distinction has become less clear as B/S and C/S are gradually merging.** Many applications support multiple platforms, making HTTP generally suitable for external communications, while RPC protocols are used for communication within internal clusters.
- RPC actually predates HTTP and tends to perform better than the currently mainstream HTTP/1.1, which is why most companies still utilize RPC internally.
- **HTTP/2** has been optimized upon **HTTP/1.1**, potentially outperforming many RPC protocols; however, it was introduced only in recent years, making it more challenging to replace RPC quickly.

<!-- @include: @article-footer.snippet.md -->
