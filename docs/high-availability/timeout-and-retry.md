---
title: Timeout & Retry Explained
category: High Availability
icon: retry
---

Due to uncertainties such as network issues, internal bugs in systems or services, server crashes, and operating system failures, our systems or services can never guarantee constant availability.

To minimize the impact of failures in systems or services, we need to utilize **Timeout** and **Retry** mechanisms.

Explaining the timeout and retry mechanisms is actually quite simple, as they are not complex concepts.

Although the ideas behind timeout and retry mechanisms are straightforward, they are indeed very practical. Most systems or services you encounter that involve remote calls will apply timeout and retry mechanisms. This is especially important for microservices systems, where correctly setting timeouts and retries is crucial. Monolithic services typically only involve network calls to databases, caches, third-party APIs, and middleware, while microservices systems also involve network calls between various services.

## Timeout Mechanism

### What is the Timeout Mechanism?

The timeout mechanism refers to the cancellation of a request if it exceeds a specified time (e.g., 1 second) without being processed, resulting in a specified exception or error being thrown (e.g., `504 Gateway Timeout`).

Timeouts can generally be categorized into the following two types:

- **Connect Timeout**: The maximum wait time for the client to establish a connection with the server.
- **Read Timeout**: The maximum time the client waits for the server to complete processing the request after a connection has been established. In practical projects, we often focus more on read timeouts.

Some connection pool client frameworks may also have connection acquisition timeouts and idle connection cleanup timeouts.

If timeouts are not set, it can lead to an explosion of server connections and a backlog of requests.

These accumulated connections and requests consume system resources, affecting the processing of new incoming requests. In severe cases, it can even bring down the entire system or service.

I encountered a similar issue in a previous project where the entire website could not process requests normally, and the server load was nearly maxed out. The cause was found to be incorrect timeout settings combined with exceptions in client request handling, leading to server connections approaching 400,000+. Such a backlog of connections directly caused the system to crash.

### How Should Timeout Values Be Set?

Determining the appropriate timeout duration is a challenge! Setting the timeout value too high or too low both carry risks. If set too high, it reduces the effectiveness of the timeout mechanism; for example, if you set the timeout to 10 seconds, then the timeout becomes meaningless, and the system may still experience a backlog of slow requests. If set too low, it may lead to a situation where, under certain conditions (e.g., a sudden increase in requests), a large number of requests are retried (timeouts are usually combined with retries), further increasing the pressure on the system or service, potentially leading to a complete system failure.

In general, we recommend setting the read timeout to **1500ms**, which is a relatively universal value. If your system or service is sensitive to latency, the read timeout can be shortened from the **1500ms** baseline. Conversely, it can also be extended, but it is best not to exceed **1500ms**. The connect timeout can be set a bit longer, ideally within **1000ms to 5000ms**.

There is no silver bullet! The specific timeout value should be adjusted and optimized based on the actual project requirements and conditions.

Furthermore, referring to [Meituan's dynamic configuration of Java thread pool parameters](https://tech.meituan.com/2020/04/02/java-pooling-pratice-in-meituan.html), we can also make timeouts configurable parameters rather than fixed values. A simple approach is to place the timeout values in a configuration center. This way, we can dynamically adjust the timeout values based on the state of the system or service.

## Retry Mechanism

### What is the Retry Mechanism?

The retry mechanism is generally used in conjunction with the timeout mechanism, referring to the repeated sending of the same request to avoid transient and occasional failures.

Transient failures can be simply understood as faults that occur momentarily in the system and are not persistent. Occasional failures can be understood as faults that occur infrequently under certain conditions.

The core idea of retrying is to consume server resources to maximize the probability of successfully processing a request. Since transient and occasional failures are rare, the resource consumption from retries is almost negligible.

### What Are Common Retry Strategies?

There are two common retry strategies:

1. **Fixed Interval Retry**: Each retry uses the same time interval, such as retrying every 1.5 seconds. The advantage of this strategy is its simplicity; there is no need to consider the relationship between retry count and time, nor maintain additional state information. However, the downside is that it may lead to retries being too frequent or too sparse, affecting system performance and efficiency. If the retry interval is too short, it may put excessive pressure on the target system, leading to
