---
title: Summary of Common Issues with RocketMQ
category: High Performance
tag:
  - RocketMQ
  - Message Queue
---

> [This article is contributed by FrancisQ!](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485969&idx=1&sn=6bd53abde30d42a778d5a35ec104428c&chksm=cea245daf9d5cccce631f93115f0c2c4a7634e55f5bef9009fd03f5a0ffa55b745b5ef4f0530&token=294077121&lang=zh_CN#rd) Compared to the original text, the following improvements have been made:
>
> - [Analyzed the reasons for RocketMQ's high-performance read and write and the specific implementation of sequential consumption](https://github.com/Snailclimb/JavaGuide/pull/2133)
> - [Added introductions to message types, consumer types, consumer groups, and producer groups](https://github.com/Snailclimb/JavaGuide/pull/2134)

## Introduction to Message Queues

A message queue, as the name suggests, is a queue that stores messages. I won't explain what a queue is; please don't tell me you don't even know what a queue is!

So the question is not what a message queue is, but rather **why do message queues exist? What can message queues be used for? What benefits do they bring? Do message queues have side effects?**

### Why Do Message Queues Exist?

Message queues are considered an essential skill for backend developers because **distributed applications inevitably involve communication between various systems**, and this is where message queues come into play. It can be said that the emergence of distributed systems is the foundation of message queues, and distributed systems are quite an old concept, making message queues also a very old middleware.

### What Can Message Queues Be Used For?

#### Asynchronous Communication

You might argue that communication between applications can be solved without message queues. Why insert a message queue into the middle of a good communication process? Can't I communicate directly?

Good point 👍, you've introduced the concept of **synchronous communication**. For example, `Dubbo`, which is widely used in the industry, is an `RPC` framework suitable for synchronous communication between various systems.

Let me give you an example 🌰. Suppose we have a ticketing system where the requirement is that users receive a text message after completing their purchase.

![](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/16ef37fee7e09230.jpg)

If we omit the time consumed by network communication, let's say the ticketing system takes 150ms to process, and the SMS system takes 200ms, then the total processing time is 150ms + 200ms = 350ms.

At first glance, there seems to be no problem. However, upon closer inspection, you might feel something is off. When the user completes the purchase in the ticketing system, they have already finished the purchase. Yet, through synchronous calls, the entire request is unnecessarily prolonged because we have to wait for the SMS system, which is not essential; it merely enhances user experience. The entire call process feels a bit **top-heavy**; purchasing is not a time-consuming process, but now, due to synchronous calls, we have to wait for the more time-consuming operation of sending the SMS to return a result. What if I add an email notification?

![](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/16ef380429cf373e.jpg)

Now the entire system's call chain has lengthened, and the total time has become 550ms.

When we were students and had to queue in the cafeteria, we were in a synchronous model with the cafeteria lady.

We need to tell the cafeteria lady: "Sister, please add a chicken leg, and some spicy potato shreds, and pour some sauce on top, and give me extra rice 😋😋😋." Oh\~~~ To eat more, how disgusting.

Then the lady helps us with the rice and dishes, and we can't help but swallow as we watch her trembling hands and the falling potato shreds.

Finally, we take the food from her and go look for a seat...

Reflecting on it, after we send the necessary information to the lady, we are **synchronously waiting for her to prepare our food**. We only added chicken legs and potato shreds; what if I add a tomato beef brisket and chives with eggs? Wouldn't the process of the lady preparing the food take longer, and our waiting time would also increase accordingly?

Later, when we started working and had money to eat at restaurants, we would tell the waiter to bring a bowl of beef noodles with a poached egg \*\*(sending
