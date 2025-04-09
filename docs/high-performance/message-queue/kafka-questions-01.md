I don't...
Title: Kafka Summary of Common Issues
Category: High Performance
Tag:

- Message Queue.
  I don't...

# Kafka Foundation

What is Kafka? What are the main applications?

Kafka is a distributed streaming platform. What the hell does that mean?

The flow platform has three key functions:

1. **News Queue**: Publish and subscribe to news streams, a function similar to news queues, which is why Kafka is also classified as a news queue.
1. **Bad, Durable Way of Storing the Record Stream**: Kafka will persist the message to disk, effectively avoiding the risk of loss of the message.
1. **Flow Processing Platform**: Handled at the time of the news release, Kafka provides a complete current processing library.

Kafka has two main applications:

1. **News Queue**: Development of a real-time flow data conduit to secure data between systems or applications.
1. **Data Processing**: Build real-time streaming data-processing programs to convert or process data flows.

What's the advantage of Kafka compared to other news queues?

Now that we've been talking about Kafka, we've agreed that it's a very good news line, and we'll always compare it to RocketMQ and RabbitMQ. I think the main advantages of Kafka over other news lines are as follows:

1. **Extraordinary Performance**: Based on Scala and Java language development, the design uses a large number of mass-processing and staggered ideas, with a maximum of tens of millions of levels of information per second.
1. **Ecosystem Compatibility is Unmatched**: Kafka's compatibility with surrounding ecosystems is one of the best, especially in the area of large data and current calculations.

In fact, in the early days, Kafka was not a well-qualified news queue, and in the early days, Kafka was like a kid in a shirt, and was not functioning well and had a few problems, such as losing information and not ensuring its reliability. Of course, it also has a great deal to do with LinkedIn’s first development of Kafka’s logs for mass handling. Ha-ha-ha-ha-ha. It was not the first time that they were in the news line, but who knew that they had taken a place in the news line.

As a result, the panels were gradually improved by Kafka. So, **Kafka's claim that the news line is unreliable is outdated!**

Do you understand the queue model? Do you know about Kafka's information model?

> Extracurricular: The early JMS and AMQPs are relevant standards by authoritative organizations in the field of news services, as I described in the article in [JavaGuide](https://github.com/Snailclimb/JavaGuide) (https://github.com/Snailclimb/JavaGuide#%E6%95%B0.6%E8D%A%E%98%A%E4%EF%A1%A4%E4%A4%A4%B8%Ad%E9%E9%B4%B4%B4%B%B6). However, the evolution of these standards has not kept pace with the pace of the news queue, and they have in fact become obsolete. So there may be a situation where different news queues have their own set of information models.

## Queue Model: Early Message Model

[Queue Model](https://oss.javaguide.cn/github/javaguide/high-performance/message-Queue/%E9.9%98%9F%E5.8%97%E6%A6%A1%A5%E5%E9%8B23.png)

**The use of Queue as a Queue medium to satisfy the producer-consumer model, a message that can only be used by one consumer and that is not consumed is kept in the queue until it is consumed or times out.** For example, if we producers send 100 messages, the two consumers will consume half each of them in the order in which the message is sent (i.e. you and me). I'm not sure.

**Problems with the Queue Model:**

If we exist, we need to distribute the information produced by the producers to multiple consumers, and every consumer receives the full information.

This situation makes the queue model difficult to resolve. A lot of the better people say, "We can create a separate line for each consumer, so that producers can send more than one." This is a very stupid practice, a waste of resources and contrary to the purpose of using news queues.

#### Release - Subscription Model: Kafka Message Model

The issue-subscription model is primarily intended to solve problems with the queue model.

(https://oss.javaguide.cn/java-guide-blog/%E5%8F%91%E5%E5%E8%E8%E8%A2%A9%E9%E98%E6%A8%A1%E5%E5%9E%8B.png)

Publish subscription model (Pub-Sub) uses **theme (Topic)** as a message carrier similar to **broadcast mode**; the publisher issues a message that is sent by theme to all subscribers, \*\*users who subscribe after a message is broadcast cannot
