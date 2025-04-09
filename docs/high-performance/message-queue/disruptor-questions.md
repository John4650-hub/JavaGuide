I don't...
Title: Disruptor Summary of Common Problems
Category: High performance
Tag:

- Message queue.
  I don't...

Disruptor is a relatively cold point of knowledge, but if you use Disruptor in your project experience, you'll probably be asked in interviews.

One of the ballmates' previous contributions, which involved a number of Disruptor's questions, sent the article: It's good to get a byte, treasure, and a lot of work. [Link](https://mp.weixin.qq.com/s/C5QMjwEb6pzXACqZsyqC4A)

![Image](https://oss.javaguide.cn/github/javaguide/high-perform/message-queue/disruptor-interview-quests.png)

The article can be seen as a brief summary of Disruptor, and each of the questions will not go too far, mainly for interviews or a quick look at Disruptor.

# Disruptor what?

Disruptor is an open-source high-performance RAM queue originally designed to address memory performance and memory security issues, developed by the British Foreign Exchange Trading Company LMAX.

According to the official Disruptor, based on the system developed by Disruptor LMAX (the new retail financial transaction platform), a one-way route supports 6 million orders per second. Martin Fowler, in an article written in 2011 [The LMAX Architecture](https://martinfowler.com/articles/lmax.html), dedicated a presentation to the architecture of this LMAX system, which is interesting to see.

After LMAX spoke at QCon in 2010, Disruptor received industry attention and received the 2011 Oracle official Duke's Choice Award.

![Image](https://oss.javaguide.cn/github/javaguide/high-perform/message-queue/640.png)

> "Duke's Choice Award" aims to recognize the most influential Java technology applications developed by individuals or companies worldwide over the past year, hosted by Oracle. The gold is very high!

I specifically found the article that Oracle officially published that year on the Duke's Choice Awards project ([Link](https://blogs.oracle.com/java/post/the-winners-are-the-dukes-choice-award)). As can be seen from the text, the same year the prize was awarded to the prestigious projects Netty and JRebel.

Oracle's Choice Awards ![Image](https://oss.javaguide.cn/javaguide/image-202110151523898.png)

Disruptor offers functional advantages similar to those of Kafka and RocketMQ, but as such, JVM (RAM).

- GitHub address: [Link](https://github.com/LMAX-Exchange/disruptor)
- Official curriculum: [Link](https://lmax-exchange.github.io/disruptor/user-guide/index.html)

With regard to the use of Disruptor in the Spring Boot project, the article can be read as follows: [Spring Boot + Disruptor Introduction to the Field](https://mp.weixin.qq.com/s/0iG5brK3bYF0BgSjX4jRia).

# Why use Disruptor?

Disruptor primarily addressed the safety of the performance and memory of the JDK built-in thread-safe queue.

**The thread-safe queues common in JDK are as follows:**

| Queue Name              | Lock Type           |
| ----------------------- | ------------------- |
| `ArrayBlockingQueue`    | `ReentrantLock`     |
| `LinkedBlockingQueue`   | `ReentrantLock`     |
| `LinkedTransferQueue`   | `CAS`               |
| `ConcurrentLinkedQueue` | `Lock-free` (`CAS`) |

As can be seen from the above table, these queues are either locked or lock-free. Locked queues are bound to affect performance, and lock-free queues run the risk of memory spills.

So, normally, we don't recommend the use of the JDK built-in thread-safe queue.

**Disruptor is different! It also ensures that the queue is bounded without a lock and that the thread is safe.**

The following is a comparison of the latency histograms of Disruptor and ArrayBlockingQueue provided by the Disruptor Network.

![Disruptor Latency Histogram](https://oss.javaguide.cn/github/javaguide/high-perform/message-queue/disruptor-latency-histogram.png)

Disruptor is really fast; about why it's so fast will be introduced later.

In addition, Disruptor offers a wealth of extension functions such as support for bulk operations and support for a variety of waiting strategies.

# Kafka and Disruptor what's the difference?

- **Kafka**: Distributed message queue, usually used for transmission between systems or services, can also be used as a stream processing platform.
- **Disruptor**: Memory-level message queue, usually used to transmit messages within the system between threads.

# Which components use Disruptor?

There are a lot of open-source projects for Disruptor, for example:

- **Log4j2**: Log4
