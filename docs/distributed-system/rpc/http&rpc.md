I don't...
title: Why RPC with HTTP protocol?
Category: Distribution
Tag:

- Rpc.
  I don't...

> This paper is from [White](https://juejin.cn/user/400187805742287), in the original language: <https://juejin.cn/post/718282245605883934>.

I remember my first contact with the RPC protocol when I was at work, and it was a bad time.

So we went online.

A lot of the explanations are very official, and I'm sure you've seen them on a variety of platforms, and they're not explained, and they're explaining another concept that we don't know. **People who understand don't need to look; people who don't understand can't understand.**

It's like it's in the fog. I know.

In order to avoid a strong **ugly trial fatigue**, today we try to rephrase it.

From TCP

As a programmer, assuming that we need to send data from an A computer process to a B computer process, we usually do programming in code using sockets.

At this point in time, we'll have to choose between **TCP and UDP. TCP is reliable; UDP is unreliable.** Unless you're a godly programmer like Ma (the early QQ used UDP in large numbers), with a little reliability requirement, ordinary people are generally incompetent with TCP.

Like down there.

```ini
fd = socket(AF_INET, SOCK_STREAM, 0);
```

`SOCK_STREAM` means the transmission of data using **bytes**, which, by implication, is **TCP protocol**.

Once the socket is defined, we can happily operate this socket, for example, by `bind()` binding IP ports and `connect()` initiating a connection.

!\[Image\](~tplv-k3u1fbpfpcp-zoom-in-crop-mark:3024:0:0:0 awebp.png)

Once the connection is established, we can use `send()` to send data and `recv()` to receive data.

Is that enough for a pure TCP connection?

No, it'll be a problem.

What's wrong with using naked TCP?

TCP has three characteristics: **connectivity**, **reliability**, based on **bytes**.

!\[Image\](~tplv-k3u1fbpfpcp-zoom-in-crop-mark:3024:0:0:0:0 awebp.png)

These three features are really summarized **very finely**, and we don't have a white back.

Every feature can talk about an article, and today we need to focus on this **based on bytes**.

Bytes can be understood as binary data flowing in a two-way channel, i.e., **01 string**. There's no border between the naked TCP and the 01s. You don't know where the whole story is.

![01 binary bytes](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/b82d4fcd0c4497986c93c1750d7~tplv-k3u1fbppfcpzoom-in-crop-mark:3024:0:0:0:0awebp.png)

Because this has no boundaries, when we choose to use TCP to send **"Sharlow" and "Serious Troubles,"** the receiving side received **"Sharlott Troubles."** \*\*At this point, the receiving side doesn't distinguish between you trying to express **"Charlow" + "Errorious"** or **"Charlotte" + "Trouble."**

![Information comparison](https://oss.javaguide.cn/github/javaguide/distributed-system/rpc/4e120d0f115241958556565f693e744a3a~tplv-k3u1fpfpcp-zoom-in-crop-mark:3024:0:0:0awebp.png)

This is the so-called **sticky-package issue,** which has been discussed in a special [article](https://mp.weixin.qq.com/s/0-YBxU1cSbDdzcZEZjmQYA) before.

The purpose of this is to tell you that naked TCP can't be used directly, and you need to add some **custom rules** to distinguish **message boundaries** on this basis.

So we wrap up every data we're going to send, for example, by adding a **header,** where it is clear how long a complete package is, depending on the length, to continue to receive data, and then they're the **messages that we really want to transmit.**

!\[Image\](~tplv-k3u1fbpfpcp-zoom-in-crop-mark:3024:0:0:0:0 awebp.png)

And the **messages** that are mentioned here can also be used for a variety of things, such as whether the message has been compressed or not, as long as it's all agreed upon, as long as we know each other. \*\*

Each TCP-based project may define a set of protocols such as this, and they may
