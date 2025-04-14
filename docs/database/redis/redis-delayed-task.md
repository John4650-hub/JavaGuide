---
title: How to Implement Delayed Tasks Based on Redis
category: Database
tag:
  - Redis
---

The functionality of implementing delayed tasks based on Redis consists of the following two solutions:

1. Redis Expiration Event Listening
1. Built-in Delayed Queue in Redisson

During the interview, you can first mention that you considered these two solutions, but ultimately found that the Redis expiration event listening approach has many issues, which led you to choose the built-in DelayedQueue in Redisson.

At this point, the interviewer may ask you some related questions, which we will cover later, so it's good to prepare in advance.

Additionally, besides the issues mentioned below, it is recommended that you review all common issues related to Redis, as the interviewer may also ask some other questions about Redis.

### What is the Principle of Implementing Delayed Task Functionality through Redis Expiration Event Listening?

Redis 2.0 introduced the publish/subscribe (pub/sub) feature. In pub/sub, a concept called **channel** is introduced, which is somewhat similar to **topic** in message queues.

pub/sub involves two roles: publisher and subscriber (also known as consumers):

- The publisher delivers messages to a specified channel using `PUBLISH`.
- Subscribers subscribe to the channels of their interest using `SUBSCRIBE`, and they can subscribe to one or more channels.

![Redis Publish/Subscribe (pub/sub) Functionality](https://oss.javaguide.cn/github/javaguide/database/redis/redis-pub-sub.png)

In the pub/sub model, the producer needs to specify which channel the message is sent to, while consumers subscribe to the corresponding channel to receive messages.

Redis has many default channels, which are messages sent by Redis itself rather than our own code. One of them, `__keyevent@0__:expired`, is a default channel responsible for listening to the expiration events of keys. This means that when a key expires, Redis will publish an expiration event to the channel `__keyevent@<db>__:expired`.

We only need to listen to this channel to receive messages about expired keys, thus achieving the delayed task functionality.

This feature is officially referred to as **keyspace notifications** by Redis, which serves to monitor changes in Redis keys and values in real-time.

### What Are the Defects of Implementing Delayed Task Functionality through Redis Expiration Event Listening?

**1. Poor Timeliness**

A paragraph in the official documentation explains the reason for poor timeliness, reference: <https://redis.io/docs/manual/keyspace-notifications/#timing-of-expired-events>.

![Redis Expiration Events](https://oss.javaguide.cn/github/javaguide/database/redis/redis-timing-of-expired-events.png)

The core of this statement is that expiration event messages are published when the Redis server deletes the key, not immediately when a key expires.

We know there are two common strategies for deleting expired data:

1. **Lazy Deletion**: It only checks for expiration when the key is accessed. This is friendly to the CPU but may result in many expired keys not being deleted.
1. **Periodic Deletion**: A batch of keys is randomly selected to execute the deletion of expired keys at intervals. Moreover, Redis limits the duration and frequency of deletion operations to reduce the impact on CPU time.

Periodic deletion is more memory-friendly, while lazy deletion is more CPU-friendly. Each has its advantages, which is why Redis adopts **periodic deletion combined with lazy/eager deletion**.

Therefore, there might be scenarios where I set an expiration time for a key, but the key has not been deleted by the specified time and thus did not publish an expiration event.

**2. Message Loss**

Messages in Redis's pub/sub mode do not support persistence, which is different from message queues. In Redis's pub/sub mode, the publisher sends messages to a specified channel, and subscribers listen to the corresponding channel to receive messages. When there are no subscribers, messages are directly discarded and will not be stored in Redis.

**3. Message Duplicate Consumption in Multiple Service Instances**

Redis's pub/sub mode currently only supports broadcasting, which means that when a producer publishes a message to a specific channel, all consumers listening to that channel can receive it.

In this case, we need to be aware of the issue of multiple service instances processing messages repeatedly, which increases both code development and maintenance difficulty.

### What Is the Principle of Redisson Delayed Queue? What Are Its Advantages?

Redisson is an open-source Redis client for the Java language, providing many out-of-the-box features, such as various implementations of distributed locks and delayed queues.

We can use Redisson's built-in delayed queue RDelayedQueue to implement the delayed task functionality.

Redisson's delayed queue RDelayedQueue is implemented based on Redis's SortedSet. SortedSet is an ordered collection where each element can have a score set that represents the weight of that element. Redisson uses this feature to insert tasks that need to be executed with a delay into the SortedSet and sets their corresponding expiration time as the score.

Redisson periodically uses the `zrangebyscore` command to scan for expired elements in the SortedSet, then removes these expired elements from the SortedSet and adds them to the ready message list. The ready message list is a blocking queue that consumers can listen to when new messages enter. This avoids consumers polling the entire SortedSet, thereby improving execution efficiency.

Compared to the Redis expiration event listening method for implementing delayed tasks, this approach has the following advantages:

1. **Reduced Possibility of Message Loss**: Messages in the DelayedQueue are persisted, so even if Redis crashes, based on the persistence mechanism, only a small number of messages may be lost, which has a minimal impact. Of course, you can also use database scanning as a compensation mechanism.
1. **No Duplicate Consumption Issue**: Every client retrieves tasks from the same target queue, eliminating the issue of duplicate consumption.

Compared to Redisson's built-in delayed queue, message queues can achieve higher throughput and stronger reliability by ensuring message consumption reliability and controlling the number of message producers and consumers, making delayed messages in message queues the preferred solution in practical projects.
