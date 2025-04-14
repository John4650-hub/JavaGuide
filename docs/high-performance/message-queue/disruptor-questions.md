---
title: Summary of Common Questions About Disruptor
category: High Performance
tag:
  - Message Queue
---

Disruptor is a relatively niche topic, but if you've used Disruptor in your project experience, it's likely you'll be asked about it in an interview.

A fellow player previously submitted an interview experience (for external recruitment) that included some Disruptor-related questions. You can find the article here: [Achieved my dream! Successfully received offers from major companies like ByteDance, Taobao, and Pinduoduo!](https://mp.weixin.qq.com/s/C5QMjwEb6pzXACqZsyqC4A).

![](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/disruptor-interview-questions.png)

This article can be seen as a simple summary of Disruptor. Each question does not delve too deeply and is mainly focused on interviews or a quick overview of Disruptor.

## What is Disruptor?

Disruptor is an open-source high-performance memory queue, originally developed to solve performance and memory safety issues associated with memory queues by LMAX, a foreign exchange trading company in the UK.

According to the official introduction of Disruptor, the LMAX system developed based on Disruptor (a new retail financial trading platform) can support 6 million orders per second with a single thread. Martin Fowler wrote an article in 2011 [The LMAX Architecture](https://martinfowler.com/articles/lmax.html) specifically introducing the architecture of this LMAX system, which may be of interest.

After LMAX’s presentation at QCon in 2010, Disruptor received attention from the industry and won the Oracle official Duke's Choice Award in 2011.

![](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/640.png)

> The "Duke's Choice Award" aims to recognize the most influential Java technology applications developed by individuals or companies worldwide in the past year, and is hosted by Oracle. It holds significant value!

I specifically found the Oracle official article announcing the Duke's Choice Award winners that year (article link: <https://blogs.oracle.com/java/post/and-the-winners-arethe-dukes-choice-award>). From the text, it can be seen that other well-known projects such as Netty and JRebel also received this award that year.

![2011 Oracle Official Duke's Choice Awards](https://oss.javaguide.cn/javaguide/image-20211015152323898.png)

The capabilities provided by Disruptor are similar to those of distributed queues like Kafka and RocketMQ, but its scope is JVM (memory).

- GitHub link: <https://github.com/LMAX-Exchange/disruptor>
- Official tutorial: <https://lmax-exchange.github.io/disruptor/user-guide/index.html>

To learn how to use Disruptor in a Spring Boot project, you can refer to this article: [Spring Boot + Disruptor Practical Introduction](https://mp.weixin.qq.com/s/0iG5brK3bYF0BgSjX4jRiA).

## Why Use Disruptor?

Disruptor mainly addresses the performance and memory safety issues of the JDK's built-in thread-safe queues.

**The commonly used thread-safe queues in the JDK are as follows**:

| Queue Name              | Lock                     | Bounded/Unbounded |
| ----------------------- | ------------------------ | ----------------- |
| `ArrayBlockingQueue`    | Locked (`ReentrantLock`) | Bounded           |
| `LinkedBlockingQueue`   | Locked (`ReentrantLock`) | Bounded           |
| `LinkedTransferQueue`   | Lock-Free (`CAS`)        | Unbounded         |
| `ConcurrentLinkedQueue` | Lock-Free (`CAS`)        | Unbounded         |

As can be seen from the table above, these queues are either bounded and locked or unbounded and lock-free. The locked queues inevitably affect performance, while unbounded queues face the risk of memory overflow.

Therefore, in general, we do not recommend using the JDK's built-in thread-safe queues.

**Disruptor is different! It guarantees that the queue is bounded while being lock-free, and it is still thread-safe.**

The following image is a comparison of latency histograms between Disruptor and ArrayBlockingQueue provided by the Disruptor official website.

![disruptor-latency-histogram](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/disruptor-latency-histogram.png)

Disruptor is indeed very fast, and we will introduce why it is so fast later.

Additionally, Disruptor also provides a rich set of extension capabilities, such as supporting batch operations and multiple wait strategies.

## What Are the Differences Between Kafka and Disruptor?

- **Kafka**: A distributed message queue typically used for message transmission between systems or services and can also be used as a stream processing platform.
- **Disruptor**: A memory-level message queue commonly used for message transmission between threads within a system.

## Which Components Use Disruptor?

There are quite a few open-source projects that utilize Disruptor. Here are a few examples:

- **Log4j2**: Log4j2 is a commonly used logging framework that implements asynchronous logging based on Disruptor.
- **SOFATracer**: SOFATracer is an open-source distributed application tracing tool developed by Ant Financial, which uses Disruptor for asynchronous logging.
- **Storm**: Storm is an open-source distributed real-time computation system that uses Disruptor for message passing within the same worker process (between threads on the same Storm node, without the need for network communication).
- **HBase**: HBase is a distributed column storage database system that uses Disruptor to improve write concurrency performance.
- ……

## What Are the Core Concepts of Disruptor?

- **Event**: You can think of an Event as a message object that is stored in the queue waiting to be consumed.
- **EventFactory**: The event factory is used to produce events that we need when initializing the `Disruptor` class.
- **EventHandler**: Events are processed in the corresponding Handler, which can be understood as the consumer in the producer-consumer model.
- **EventProcessor**: EventProcessor holds the sequence of a specific consumer and provides an event loop for invoking the implementation of event processing.
- **Disruptor**: The production and consumption of events require the `Disruptor` object.
- **RingBuffer**: RingBuffer (circular array) is used to store events.
- **WaitStrategy**: The wait strategy determines how the event consumers wait for new events to arrive when there are no events to consume.
- **Producer**: The producer refers generically to user code that calls the `Disruptor` object to publish events; Disruptor does not define a specific interface or type.
- **ProducerType**: Specifies whether it is a single event publisher mode or multiple event publisher mode (the term publisher is similar to producer, and I personally prefer to use publisher).
- **Sequencer**: The Sequencer is the true core of Disruptor. This interface has two implementation classes: `SingleProducerSequencer` and `MultiProducerSequencer`, which define a concurrent algorithm for fast and correct data transmission between producers and consumers.

The following image is extracted from the Disruptor official website and shows an example of using Disruptor in the LMAX system.

![Example of Using Disruptor in LMAX System](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/disruptor-models.png)

## What Are the Wait Strategies of Disruptor?

**WaitStrategy** determines how event consumers wait for new events to arrive when there are no events to consume.

Common wait strategies include the following:

![Disruptor Wait Strategy](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/DisruptorWaitStrategy.png)

- `BlockingWaitStrategy`: Implemented using `ReentrantLock` + `Condition` for waiting and waking operations. The implementation code is quite simple and is the default wait strategy of Disruptor. Although it is the slowest, it is also the option with the lowest and most stable CPU usage and is recommended for production environments.
- `BusySpinWaitStrategy`: Provides good performance but has the risk of continuous spinning, which can cause the CPU load to reach 100% if used improperly, so caution is advised.
- `LiteBlockingWaitStrategy`: A lightweight wait strategy based on `BlockingWaitStrategy` that skips the waking operation when there is no lock contention, but the author mentions that testing is insufficient and therefore it's not recommended for use.
- `TimeoutBlockingWaitStrategy`: A wait strategy with a timeout that executes specified processing logic after the timeout.
- `LiteTimeoutBlockingWaitStrategy`: A strategy based on `TimeoutBlockingWaitStrategy` that skips wakening operations when there is no lock contention.
- `SleepingWaitStrategy`: A three-phase strategy — in the first phase, it spins; in the second phase, it executes Thread.yield to give up the CPU; in the third phase, it sleeps for a specific time, repeating the sleep.
- `YieldingWaitStrategy`: A two-phase strategy — in the first phase, it spins; in the second phase, it executes Thread.yield to yield the CPU.
- `PhasedBackoffWaitStrategy`: A four-phase strategy — in the first phase, it spins a specified number of times; in the second phase, it spins for a specified duration; in the third phase, it executes `Thread.yield` to yield the CPU; in the fourth phase, it calls the member variable's `waitFor` method, which can be set to one of `BlockingWaitStrategy`, `LiteBlockingWaitStrategy`, or `SleepingWaitStrategy`.

## Why is Disruptor So Fast?

- **RingBuffer (Circular Array)**: The internal RingBuffer of Disruptor is implemented via an array. Since all elements of this array are created at once during initialization, these element memory addresses are generally continuous. The benefit of this is that as the producer continuously inserts new event objects into the RingBuffer, the memory addresses of these event objects can remain contiguous, thus utilizing the locality principle of CPU caches and loading adjacent event objects into the cache together to improve program performance. This is similar to MySQL's pre-read mechanism, which pre-loads several continuous pages into memory. Additionally, the RingBuffer, based on the array, supports batch operations (processing multiple elements at once) and avoids frequent memory allocation and garbage collection (the RingBuffer is a fixed-size array, so when a new element is added to the array, if the array is full, the new element will overwrite the oldest element).
- **Avoided False Sharing Issues**: CPU caches are managed according to Cache Lines, with a typical Cache Line size around 64 bytes. Disruptor ensures that the target field occupies a single Cache Line by adding byte padding (56 bytes before and after the target field), thereby avoiding the false sharing problem. Additionally, to ensure that the array storing the data in the RingBuffer occupies a cache line, the design of the array includes invalid padding (128 bytes) plus valid data.
- **Lock-Free Design**: Disruptor employs a lock-free design, eliminating the competition and delays associated with traditional locking mechanisms. The lock-free implementation of Disruptor is relatively complex and is primarily based on technologies such as CAS, memory barriers, and RingBuffer.

In summary, the rapid performance of Disruptor stems from a comprehensive series of optimization strategies, effectively utilizing the characteristics of modern CPU cache structures while avoiding common concurrency issues and performance bottlenecks.

For a detailed introduction to the principles of Disruptor's high-performance queue, refer to this article: [Analysis of Disruptor High-Performance Queue Principles](https://qin.news/disruptor/) (which references the Meituan tech team's article on [High-Performance Queue - Disruptor](https://tech.meituan.com/2016/11/18/disruptor.html)).

🌈 As an additional note: **Why does having contiguous memory addresses for object elements in an array improve performance?**

CPU caches enable faster reading speeds by storing recently used data in high-speed caches and using prefetching mechanisms to load adjacent memory data ahead of time, leveraging the principle of locality.

In computer systems, the CPU primarily accesses high-speed caches and memory. Caches are very fast and relatively small in capacity, usually organized into multiple levels, denoted as L1, L2, and L3 (representing levels of cache). The closer the cache is to the CPU, the faster and smaller its capacity tends to be. In contrast, memory has a relatively large capacity but slower speeds.

![CPU Cache Model Diagram](https://oss.javaguide.cn/github/javaguide/java/concurrent/cpu-cache.png)

To accelerate data reading, the CPU first loads data from memory into the cache; if the same data needs to be accessed again, it can be read directly from the cache without needing to access memory again. This is known as a **cache hit**. Additionally, to utilize **locality**, the CPU will prefetch adjacent memory data based on previously accessed memory addresses, as consecutive memory addresses are usually accessed frequently in programs; this increases the cache hit rate and, consequently, improves program performance.

## References

- Disruptor Performance Strategy - Wait Strategies: \<<http://wuwenliang.net/2022/02/28/Disruptor> Performance Strategy - Wait Strategies/>
- "Java Concurrency in Practice" - 40 | Case Analysis (3): High-Performance Queue Disruptor: <https://time.geekbang.org/column/article/98134>

<!-- @include: @article-footer.snippet.md -->
