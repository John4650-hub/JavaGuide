I don't...
title: Guide to Highly Available System Design
Category: High availability
icon:
I don't...

# What's high availability? What is the test of usability?

A system that is available most of the time and can be of service to us is a system that is highly accessible. The service is still available even when there is a hardware failure or system upgrade.

In general, how many nines do we use to judge the availability of a system? For example, 99.9999%, which means that the system has only 0.001% downtime, which is very, very, very useful! There's, of course, a system that, if available, might not even be usable.

In addition, the availability of the system can be measured by the ratio of the number of failures of a function to the total number of requests, such as 1,000 requests to a website, of which 10 failed, resulting in an availability of 99 percent.

# What would make the system unusable?

1. Hacker attacks;
1. Hardware malfunctions, such as server failure;
1. The proliferation of simultaneous user requests resulting in the loss of or partial non-availability of the entire service;
1. Poor code quality leading to memory leaks or other problems that cause the program to suspend;
1. An important component in the web architecture, such as Nginx or a database, suddenly becoming unavailable;
1. Natural disasters or man-made destruction;
1. ...

# What are the ways to improve system availability?

## Focus on the quality of the code and test at a high level

I think this is the most important aspect. Problems with code quality, such as common memory leaks and circular dependencies, are extremely damaging to the availability of the system. Everyone likes to talk about restricted flow, downgrades, and failures, but I think it's crucial to address code quality first. How to improve code quality? A practical approach is Code Review; don't worry about the extra hours spent.

In addition, there are several tools that can help improve code quality:

- [SonarQube](https://www.sonarqube.org/);
- Alibaba Open Source Java Diagnostic Tool [Arthas](https://arthas.aliyun.com/doc/);
- [Alibaba Java Code Guidelines](https://github.com/alibaba/p3c);
- IDA's own code analysis, etc.

## Use clusters to reduce single points of failure

Take Redis as an example! How do we guarantee that our Redis cache is available? The answer is to use clusters to avoid single-point failures. When we use a Redis instance as a cache, if that instance fails, the whole cache may become unavailable. Once a cluster is in use, even if one of the Redis instances hangs, another Redis instance will take over in less than a second.

## Flow control

Flow control, based on indicators such as QPS or concurrency, monitors the flow of applications and controls it when a specified threshold is reached, in order to avoid being overwhelmed by instantaneous peaks in traffic, thereby safeguarding the high availability of applications. — from [Alibaba Sentinel](https://github.com/alibaba/Sentinel "Sentinel") wiki.

## Timeout and Retry Settings

Once a user's request is unresponsive beyond a certain time, the request is dropped. This is very important, as many online system failures are caused by the absence of timeout settings or incorrect configurations. In particular, when we read from third-party services, we should implement mechanisms for timeouts and retries. Usually, when we use some RPC frameworks, they come with their own timeout and retry configurations. Failure to implement timeout settings may lead to slow responses to requests or even cause request pile-ups, preventing the system from processing requests effectively. The number of retries is typically set to three, as repeated retries do not provide benefits and may increase the pressure on the server (some scenarios may not be suitable for a retry mechanism).

## Circuit Breaker Mechanisms

In addition to timeout and retry mechanisms, circuit breaker mechanisms are also important. A circuit breaker is a system's automatic collection of resource usage and performance indicators for the services it relies on, which quickly fails when the service degrades or when the number of failed calls reaches a certain threshold, allowing the current system to immediately switch to backup services. Commonly used traffic control and circuit breaker frameworks include Netflix's Hystrix and Alibaba's Sentinel.

## Step-by-Step Processing

We don't need to worry about the final result if we move from one step to another, allowing us to return results as soon as the user requests them, while processing can continue later. However, we may need **to adapt business processes**, e.g., **users may not be able to receive confirmation of successful orders immediately after submission and need to be notified by email or text after the message queue consumer has processed the order and the inventory has been updated.** In addition to the different steps that can be achieved in the process, we often use message queues, which can improve system performance (reduce response time) and reduce system bottlenecks.

## 
