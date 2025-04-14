---
title: Detailed Explanation of Rate Limiting
category: High Availability
icon: limit_rate
---

For software systems, rate limiting refers to the restriction of request rates to prevent a sudden influx of requests from overwhelming the system. After all, the processing capacity of a software system is limited. If the number of requests exceeds its processing capacity, the software system may crash.

Rate limiting may result in user requests not being processed correctly or not being processed immediately; however, this is often the optimal solution after weighing the stability of the software system.

In real life, there are many practical applications of rate limiting. For example, queuing to buy tickets is intended to prevent a large number of users from flooding in and overwhelming the ticket seller.

## What are the Common Rate Limiting Algorithms?

Here’s a brief introduction to four very understandable and easy-to-implement rate limiting algorithms!

> Image source: An article from InfoQ [“Practical Distributed Service Rate Limiting, Already Prepared for You”](https://www.infoq.cn/article/Qg2tX8fyw5Vt-f3HH673).

### Fixed Window Counter Algorithm

A fixed window is essentially a time window. Its principle is to divide time into fixed-size windows, limiting the number of requests or the rate within each window. The fixed window counter algorithm specifies the number of requests the system can handle in a unit of time.

For example, if we specify that a certain interface in the system can only be accessed 33 times in one minute, the implementation idea of the fixed window counter algorithm is as follows:

- Divide time into fixed-size windows, here it is one window per minute.
- Set a variable `counter` to record the number of requests processed by the current interface, with an initial value of 0 (indicating that no requests have been processed in the current minute).
- For each request processed within one minute, increment `counter` by 1. Once `counter=33` (meaning the interface has been accessed 33 times in this minute), all subsequent requests will be rejected.
- At the end of one minute, reset `counter` to 0 and start counting again.

![Fixed Window Counter Algorithm](https://static001.infoq.cn/resource/image/8d/15/8ded7a2b90e1482093f92fff555b3615.png)

Advantages: Simple to implement and easy to understand.

Disadvantages:

- Rate limiting is not smooth enough. For example, if we limit a certain interface to only 30 accesses per minute, and 30 requests arrive in the first 30 seconds, then no requests can be processed in the following 30 seconds, which is undesirable and results in a poor user experience!
- It cannot guarantee the rate limiting speed, thus unable to cope with sudden surges in traffic. For instance, if we limit a certain interface to only 1000 accesses per minute, and the QPS of that interface is 500, if no requests are received in the first 55 seconds, but suddenly 1000 requests arrive in the last second, these 1000 requests cannot be processed in that second, and the system will be overwhelmed by the sudden influx of requests.

### Sliding Window Counter Algorithm

**The sliding window counter algorithm** can be considered an upgraded version of the fixed window counter algorithm, with finer granularity for rate limiting.

The optimization of the sliding window counter algorithm compared to the fixed window counter algorithm is that **it divides time into segments at a certain ratio**.

For example, if our interface limits processing to 60 requests per minute, we can divide one minute into 60 windows. Each window can only handle no more than `60(requests)/60(window)` requests per second. If the total request count in the current window exceeds the limit, no further requests will be processed.

Clearly, **the more segments the sliding window is divided into, the smoother the sliding window's movement will be, and the more accurate the rate limiting statistics will be.**

![Sliding Window Counter Algorithm](https://static001.infoq.cn/resource/image/ae/15/ae4d3cd14efb8dc7046d691c90264715.png)

Advantages:

- Compared to the fixed window algorithm, the sliding window counter algorithm can handle sudden surges in traffic.
- Compared to the fixed window algorithm, the sliding window counter algorithm has finer granularity, allowing for more precise rate limiting control.

Disadvantages:

- Similar to the fixed window counter algorithm, the sliding window counter algorithm still suffers from the issue of insufficiently smooth rate limiting.
- Compared to the fixed window counter algorithm, the sliding window counter algorithm is more complex to implement and understand.

### Leaky Bucket Algorithm

We can compare the action of sending requests to pouring water into a bucket, while the process of handling requests can be likened to water leaking from the bucket. We can pour water into the bucket at any rate, and it leaks out at a certain rate. If the water exceeds the bucket's capacity, it will
