I don't...
title: How to achieve a time extension based on Redis\
Category: Database\
Tag:

- Redis

I don't...

The function based on Redis to achieve the delayed task is based on two options:

1. Redis expired listening
1. Redisson built-in delay queue

At the interview, you can say you considered both options, but you finally found that there were many problems with the Redis expired bugging, so you finally chose the Delayed Queue, which was built in Redisson.

At this point, the interviewer may be asking you some pertinent questions, and we will mention them later.

Furthermore, apart from the issues described below, it is suggested that the Redis-related common questions be reviewed by you, not excluding that the interviewer will follow you up on some of the other Redis questions.

## Redis listens to the principles of the time-delay function of expired events?

Redis 2.0 introduces the publishing subscription (pub/sub) function. In pub/sub, a concept called **channel** was introduced, which is somewhat similar to the one in the news queue **topic**.

Pub/sub involves two roles of publisher (publisher) and subscriber (subscriber, also known as consumer):

- The publisher sends a message to the designated channel.
- The subscriber subscribes by `SUBSCRIBE` to the channel it cares about, and can subscribe to one or more channels.

![Redis issues subscriptions (pub/sub) function](https://oss.javaguide.cn/github/javaguide/database/redis/redis-pub-sub.png)

In pub/sub, producers need to specify which message to send to, and consumers subscribe to the corresponding channel to get the message.

There are a lot of default channels in Redis, and these channels are sent to them by Redis itself, not the code we created ourselves. Of these, `__keyevent@0:expired` is a default channel that is responsible for listening to the expiration of keys. That is, when a key expires, Redis will publish a key expired event in this channel.

All we have to do is listen to this channel, and we can get the expired key, and then we can get the delay.

This function, officially known as **keyspace notifications**, is used to monitor changes in Redis keys and values in real time.

## Redis, what's wrong with listening to overdue tasks?

**1. Poor timeliness**

An introduction to the official document explains the poor timeliness at <https://redis.io/docs/manual/keyspace-notifications/#timing-of-expired-events>.

![Redis Expiry](https://oss.javaguide.cn/github/javaguide/database/redis/redis-timing-of-expired-events.png)

At the heart of the sentence is that the expired event message was released when the Redis server deleted the key, rather than when a key expired.

We know that there are two strategies to remove obsolete data:

1. **Inertial delete**: data will only be checked for expiry when the key is removed. It's the most friendly for CPU, but it could cause too many expired keys not to be deleted.
1. **Periodically deleted**: at regular intervals, a batch of keys is drawn to perform the removal of expired keys. Furthermore, the bottom of Redis reduces the impact of the deletion on CPU time by limiting the length and frequency of the deletion operation.

Regular deletion is more friendly to memory and inertia is more friendly to CPU. Each one has its own, so Redis uses **periodically delete + inert/lazy**.

So there will be cases where I set the expiry time for the key, but at the specified time the key has not been deleted, and no expiry events are published.

**2. Discovery**

The news in Redis' pub/sub mode does not support permanence, unlike the news queue. In Redis' pub/sub mode, the publisher sends the message to the specified channel, and the subscriber listens to the corresponding channel to receive the message. When there is no subscriber, the message is simply discarded and will not be stored in Redis.

**3. Repeated consumption in multi-service cases**

The pub/sub model of Redis is currently only broadcast, which means that when producers send a message to a specific channel, all consumers subscribed to the relevant channel receive it.

At this point in time, we need to pay attention to multiple service examples that repeat the message, which will make code development and maintenance more difficult.

## Redisson, what's the principle of the delaying queue? What's the advantage?

Redisson is an open-source Java language Redis client that provides many open-boxing functions, such as the realization of multiple distributed locks and delay queues.

We can use the extended Queue function of RDelayedQueue, which is embedded in Redisson.

RDelayedQueue is based on SortedSet of Redis. SortedSet is an orderly collection in which each element can set a fraction representing the weight of the element. Using this feature, Redisson will need to insert the delayed task into SortedSet and set the corresponding expiry time as a fraction.

Redisson regularly uses the \`
