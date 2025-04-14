---
title: High Availability System Design Guide
category: High Availability
icon: design
---

## What is High Availability? What are the criteria for determining availability?

High availability describes a system that is available most of the time and can provide services to us. High availability means that the system remains available even during hardware failures or system upgrades.

Generally, we use the number of nines to evaluate a system's availability. For example, 99.9999% means that the system is unavailable only 0.0001% of the time during all operational hours, making it extremely high availability! Of course, there are systems with poor availability that may not even reach a single nine.

In addition, the availability of a system can also be measured by the ratio of the number of failures of a certain function to the total number of requests. For example, if a website receives 1000 requests and 10 of them fail, the availability would be 99%.

## What situations can lead to system unavailability?

1. Hacker attacks;
1. Hardware failures, such as server breakdowns.
1. A surge in concurrent users/request volume causing the entire service to crash or parts of the service to become unavailable.
1. Code smells leading to memory leaks or other issues causing the program to crash.
1. An important component of the website architecture, such as Nginx or the database, suddenly becomes unavailable.
1. Natural disasters or human destruction.
1. ……

## What methods are there to improve system availability?

### Focus on Code Quality, Strict Testing

I believe this is the most important aspect. Issues with code quality, such as common memory leaks and circular dependencies, can greatly harm system availability. Everyone likes to talk about rate limiting, degradation, and circuit breaking, but I think ensuring code quality from the source is a crucial first step. How can we improve code quality? A practical approach is Code Review; don't worry about spending an extra hour each day, as the benefits can be significant!

Additionally, here are a few tools that can effectively improve code quality:

- [Sonarqube](https://www.sonarqube.org/)；
- Alibaba's open-source Java diagnostic tool [Arthas](https://arthas.aliyun.com/doc/)；
- [Alibaba Java Code Guidelines](https://github.com/alibaba/p3c)；
- Built-in code analysis tools in IDEA.

### Use Clusters to Reduce Single Points of Failure

Let's take the commonly used Redis as an example! How can we ensure our Redis cache is highly available? The answer is to use a cluster to avoid single points of failure. When we use a single Redis instance as a cache, if that instance goes down, the entire cache service may fail. With a cluster, even if one Redis instance fails, another instance will take over in less than a second.

### Rate Limiting

Flow control involves monitoring application traffic metrics such as QPS or concurrent thread counts. When a specified threshold is reached, traffic is controlled to avoid being overwhelmed by sudden traffic spikes, thereby ensuring high application availability. — From the [alibaba-Sentinel](https://github.com/alibaba/Sentinel "Sentinel") wiki.

### Timeout and Retry Mechanism Settings

If a user request does not receive a response within a certain time, an exception should be thrown. This is very important; many online system failures occur due to the lack of timeout settings or incorrect timeout configurations. When reading from third-party services, it is especially suitable to set timeout and retry mechanisms. Generally, when using some RPC frameworks, these frameworks come with built-in timeout and retry configurations. Not setting a timeout can lead to slow response times and even request accumulation, making the system unable to process requests. The retry count is usually set to 3 times; retrying more than that is not beneficial and may increase server pressure (in some scenarios, using a failure retry mechanism may not be suitable).

### Circuit Breaker Mechanism

In addition to timeout and retry mechanism settings, the circuit breaker mechanism is also very important. The circuit breaker mechanism automatically collects resource usage and performance metrics of the dependent services. When the performance of the dependent service deteriorates or the number of failed calls reaches a certain threshold, it quickly fails, allowing the current system to switch to other backup services immediately. Commonly used flow control and circuit breaker frameworks include Netflix's Hystrix and Alibaba's Sentinel.

### Asynchronous Calls

With asynchronous calls, we do not need to worry about the final result, allowing us to return results immediately after the user request is completed. The specific processing can be done later, which is quite common in flash sale scenarios. However, after using asynchronous calls, we may need to **appropriately modify the business process to accommodate** this, such as **not immediately returning a success message to the user after submitting an order. Instead, we should notify the user of the order success via email or SMS only after the order has been processed by the message queue's order consumer process, or even after it has been shipped**. In addition to implementing
