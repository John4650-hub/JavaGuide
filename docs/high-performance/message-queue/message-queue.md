---
title: Summary of Basic Knowledge of Message Queue
category: High Performance
tag:
  - Message Queue
---

::: tip

The message queue discussed in this article primarily refers to distributed message queues.

:::

"RabbitMQ?" "Kafka?" "RocketMQ?"... In our daily study and development, we often hear the keyword message queue. I have mentioned this concept in my multiple articles. You may be an experienced user of message queues, or perhaps you are a novice unfamiliar with message queues. Regardless of your understanding, this article will help you grasp some fundamental theories of message queues.

If you are an expert, you may learn some important concepts about message queues that you have not previously noticed from this article. If you are a novice, I believe this article will serve as a stepping stone for you to open the door to message queues.

## What is a Message Queue?

We can think of a message queue as a container for storing messages. When we need to use a message, we can directly take it out from the container. Since a queue is a first-in, first-out data structure, the consumption of messages is also done in order.

![](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/message-queue-small.png)

The two parties involved in message transmission are called **producers** and **consumers**. The producer is responsible for sending messages, while the consumer is responsible for processing messages.

![Publish/Subscribe (Pub/Sub) Model](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/message-queue-pub-sub-model.png)

One important way for process communication in operating systems is through message queues. The message queues we are referring to here are slightly different; they more often indicate communication between various services and components/modules within systems, functioning as a type of **middleware**.

Wikipedia describes middleware as follows:

> Middleware is software that provides common services and capabilities to applications outside of what's offered by the operating system. It can be used to connect different applications, making communication between software components easier. Middleware sits on top of the operating system of client-server and manages computing resources and network communication.

In simple terms: **Middleware is a type of software that serves application software, which in turn serves users who do not directly interact with or use middleware.**

In addition to message queues, common middleware includes RPC frameworks, distributed components, HTTP servers, task scheduling frameworks, configuration centers, database partitioning and sharding tools, and data migration tools, among others.

A more detailed introduction to middleware can be found in a response from Alibaba's Taobao technology: <https://www.zhihu.com/question/19730582/answer/1663627873>.

With the development of distributed and microservice systems, message queues have gained greater utility in system design. Using message queues can reduce system coupling, enable asynchronous tasks, and effectively manage peak traffic. They are one of the key components in distributed and microservice systems.

## What Are the Uses of Message Queues?

Generally speaking, using message queues can bring the following three benefits to our systems:

1. Asynchronous processing
1. Traffic shaping/throttling
1. Reduced system coupling

In addition to these three points, message queues also have other application scenarios, such as implementing distributed transactions, ensuring order guarantees, and performing data stream processing.

If you are asked this question in an interview, it is usually because you have mentioned message queues in your resume. At this point, it is advisable to relate your answer to your own project experience.

### Asynchronous Processing

![Improving System Performance Through Asynchronous Processing](https://oss.javaguide.cn/github/javaguide/Asynchronous-message-queue.png)

Handling time-consuming operations included in user requests through message queues allows the corresponding messages to be sent to the queue, enabling immediate results to be returned and reducing response times, thus improving user experience. Subsequently, the system consumes the messages.

Since user request data is written into the message queue and immediately returned to the user, it is possible that the request fails during subsequent business verification or database writing operations. Therefore, **after using a message queue for asynchronous processing, it is necessary to appropriately modify business processes to accommodate this**, for instance, informing users of successful order submissions via email or SMS only after the order is truly processed by the message queue's consumer process or shipped, to avoid transaction disputes. This is similar to the process of booking train tickets or movie tickets on our smartphones.

### Traffic Shaping/Throttling

**Store the transactional messages generated by high concurrency in a short period in the message queue, and then the backend services can gradually consume these messages based on their capacity, thereby avoiding overwhelming the backend services.**

For example: In some e-commerce flash sales or promotional activities, properly using message queues can effectively mitigate the impact of a surge of orders at the start of a promotional event. As shown in the figure below:

![Traffic Shaping](https://oss.javaguide.cn/github/javaguide/%E5%89%8A%E5%B3%B0-%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97.png)

### Reducing System Coupling

Using message queues can also reduce system coupling. If there are no direct calls between modules, then adding or modifying a module impacts other modules less, enhancing system scalability.

The producer (client) sends messages to the message queue, and the consumer (server) processes the messages. The systems that need to consume messages can directly retrieve them from the message queue without coupling with other systems, clearly improving system extensibility.

![Publish/Subscribe (Pub/Sub) Model](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/message-queue-pub-sub-model.png)

**Message queues work using the publish-subscribe model, where the message sender (producer) publishes messages, and one or more message receivers (consumers) subscribe to the messages.** As seen in the above diagram, **there is no direct coupling between message senders (producers) and message receivers (consumers)**. The message sender sends the message to the distributed message queue and completes its handling of that message. The message receiver retrieves the message from the distributed message queue for further processing without needing to know where the message originated. **For new business, as long as the interested parties subscribe to the relevant messages, there is no impact on existing systems and business, thereby achieving extensibility of the website's business design**.

For instance, our e-commerce system comprises various services like user, order, finance, storage, message notification, logistics, and risk control. After a user completes an order, it needs to call services for finance (payment), storage (inventory management), logistics (delivery), message notification (notifying users of shipment), and risk control (risk assessment). With message queues, the order placement and subsequent payment, delivery, notification, etc., become decoupled; after placing an order, a message is sent to the message queue, and the necessary parties can subscribe to that message for consumption.

![](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/message-queue-decouple-mall-example.png)

Additionally, to prevent message loss due to message queue server downtime, successfully sent messages are stored on the message producer's server and are deleted only once the message has been genuinely processed by the consumer server. When the message queue server is down, the producer server will choose another server from the distributed message queue server cluster to publish the message.

**Note:** Do not think that message queues can only work using the publish-subscribe model; it is just that in the specific business context of decoupling, the publish-subscribe model is utilized. Besides the publish-subscribe model, there is also the point-to-point subscription model (where a message is consumed by only one consumer). The most commonly used model is the publish-subscribe model. Moreover, these two messaging models are provided by JMS, and the AMQP protocol offers five additional messaging models.

### Implementing Distributed Transactions

One solution for distributed transactions is MG transactions.

RocketMQ, Kafka, Pulsar, and QMQ all provide features related to transactions. Transactions allow event-stream applications to define the entire process of consuming, processing, and producing messages as an atomic operation.

For a detailed introduction, you can refer to the article [Detailed Explanation of Distributed Transactions (Paid)](https://javaguide.cn/distributed-system/distributed-transaction.html).

![Detailed Explanation of Distributed Transactions - MQ Transactions](https://oss.javaguide.cn/github/javaguide/csdn/07b338324a7d8894b8aef4b659b76d92.png)

### Order Guarantee

In many application scenarios, the order of data processing is crucial. Message queues ensure that data is processed in a specific order, making them suitable for scenarios with strict requirements for data order. Most message queues, such as RocketMQ, RabbitMQ, Pulsar, and Kafka, support ordered messages.

### Delayed/Timed Processing

Messages are not immediately consumed after being sent but are instead scheduled for processing after a specified time. Most message queues, like RocketMQ, RabbitMQ, Pulsar, and Kafka, support scheduled/delayed messages.

![](https://oss.javaguide.cn/github/javaguide/tools/docker/rocketmq-schedule-message.png)

### Instant Messaging

MQTT (Message Queuing Telemetry Transport) is a lightweight messaging protocol that adopts the publish/subscribe model and is very suitable for applications in IoT (Internet of Things) that need to operate in low bandwidth, high latency, or unreliable network environments. It supports instant messaging, maintaining stable communication even under adverse network conditions.

RabbitMQ has an MQTT plugin built-in to implement MQTT functionality (it is disabled by default and needs to be manually enabled).

### Data Stream Processing

For the massive data streams generated by distributed systems, such as business logs, monitoring data, and user behavior, message queues can collect this data in real-time or in batches and import it into big data processing engines, enabling efficient data stream management and processing.

## What Problems Can Using Message Queues Bring?

- **Decreased System Availability:** System availability is reduced to some extent. Why is that? Before introducing MQ, you didn't have to consider issues like message loss or MQ crashes, but after introducing MQ, you do!
- **Increased System Complexity:** After adding MQ, you need to ensure messages are not consumed multiple times, handle message processing failures, guarantee message delivery order, and other related issues!
- **Consistency Problems:** I mentioned above that message queues can achieve asynchronous processing, which indeed improves system response speed. However, what if the true consumers of the messages do not consume them correctly? This can lead to inconsistent data!

## JMS and AMQP

### What is JMS?

JMS (Java Message Service) is Java's messaging service, allowing JMS clients to perform asynchronous message transmission through JMS services. **The JMS (Java Message Service) API is a standard or specification for message services**, allowing application components to create, send, receive, and read messages based on the JavaEE platform. It reduces the coupling of distributed communication, making message services more reliable and asynchronous.

JMS defines five different message body formats and calling message types that allow you to send and receive data in various forms:

- `StreamMessage`: Java raw value data stream
- `MapMessage`: A set of name-value pairs
- `TextMessage`: A string object
- `ObjectMessage`: A serialized Java object
- `BytesMessage`: A byte data stream

**ActiveMQ (now deprecated) is based on the JMS specification.**

### Two Message Models in JMS

#### Point-to-Point (P2P) Model

![Queue Model](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/message-queue-queue-model.png)

Uses **Queues** as the message communication carrier; satisfies the **Producer-Consumer model** where a message can only be used by one consumer. Unconsumed messages remain in the queue until they are consumed or time out. For example, if the producer sends 100 messages, typically, two consumers will consume half of them each, following the order in which the messages were sent.

#### Publish/Subscribe (Pub/Sub) Model

![Publish/Subscribe (Pub/Sub) Model](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/message-queue-pub-sub-model.png)

The Publish-Subscribe model (Pub/Sub) uses **Topics** as the message communication carrier, similar to **broadcast mode**; when a publisher publishes a message, that message is delivered to all subscribers through the topic.

### What is AMQP?

AMQP, or Advanced Message Queuing Protocol, is a binary application layer protocol providing a unified messaging service. It is an open standard designed for message-oriented middleware and is compatible with JMS. AMQP-based clients can pass messages to message middleware without being limited by client/middleware products or different programming languages.

**RabbitMQ is based on the AMQP protocol.**

### Comparison of JMS and AMQP

|  Comparison Aspect   | JMS                                                   | AMQP                                                                                                                                                                                                                                                           |
| :------------------: | :---------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|      Definition      | Java API                                              | Protocol                                                                                                                                                                                                                                                       |
|    Cross-Language    | No                                                    | Yes                                                                                                                                                                                                                                                            |
|    Cross-Platform    | No                                                    | Yes                                                                                                                                                                                                                                                            |
| Message Type Support | Provides two message models: ① Peer-2-Peer; ② Pub/Sub | Provides five message models: ① direct exchange; ② fanout exchange; ③ topic change; ④ headers exchange; ⑤ system exchange. Essentially, the latter four are not significantly different from the JMS pub/sub model but have a more detailed routing mechanism. |
| Message Type Support | Supports various message types mentioned above        | byte[](binary)                                                                                                                                                                                                                                                 |

**Summary:**

- AMQP defines a wire-level protocol for messaging, while JMS defines the API specification. Within the Java ecosystem, multiple clients can interact through JMS without needing to modify application code, but its support for cross-platform capability is weaker. AMQP, by nature, supports cross-platform and cross-language features.
- JMS supports complex message types like `TextMessage` and `MapMessage`, whereas AMQP only supports `byte[]` message types (complex types can be serialized and sent).
- Due to the routing algorithms provided by Exchange, AMQP can offer a diverse range of routing methods for delivering messages to message queues, while JMS only supports two methods: queues and topics/subscriptions.

## Differences Between RPC and Message Queues

Both RPC and message queues are important components in distributed microservice systems. Let's briefly compare the two:

- **Use Case:** RPC primarily addresses the remote communication issue between two services, without needing to know the underlying network communication mechanism. Through RPC, we can easily invoke methods from services on remote computers as if calling local methods. Message queues are mainly used to reduce system coupling, achieve asynchronous tasks, and efficiently manage traffic shaping.
- **Communication Method:** RPC is bidirectional direct network communication, while message queues are unidirectional with an intermediary.
- **Architecture Requirements:** Message queues need to store messages, whereas RPC does not require this, as it is bidirectional direct network communication.
- **Timeliness of Request Processing:** Calls made through RPC are typically processed immediately, while messages stored in a message queue may not be processed right away.

RPC and message queues are essentially two different implementation mechanisms for network communication, serving different purposes and should not be conflated.

## Distributed Message Queue Technology Selection

### What Are the Common Message Queues?

#### Kafka

![](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/kafka-logo.png)

Kafka is a distributed streaming processing platform open-sourced by LinkedIn and has become an Apache top-level project. It was initially used for handling massive logs, later evolving into a comprehensive high-performance message queue.

Streaming processing platforms have three key functions:

1. **Message Queues:** Publishing and subscribing to message streams, similar to message queues, which is why Kafka is categorized as a message queue.
1. **Fault-Tolerant Persistent Storage of Recorded Message Streams:** Kafka persists messages to disk, effectively avoiding the risk of message loss.
1. **Streaming Processing Platform:** Processes messages during publishing, with Kafka providing a complete streaming processing library.

Kafka is a distributed system composed of servers and clients communicating through a high-performance TCP network protocol and can be deployed on bare metal hardware, virtual machines, and containers in local and cloud environments.

Before Kafka 2.8, Kafka was heavily criticized for its reliance on Zookeeper for metadata management and cluster high availability. After Kafka 2.8, KRaft mode was introduced based on the Raft protocol, eliminating the dependency on Zookeeper and greatly simplifying Kafka's architecture to allow for a more lightweight usage.

However, a note of caution: **If you want to use KRaft mode, it is recommended that you choose a higher version of Kafka, as this feature is still under continuous refinement and optimization. Kafka version 3.3.1 is the first release where KRaft (Kafka Raft) consensus protocol is marked as production-ready.**

![](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/kafka3.3.1-kraft-production-ready.png)

Kafka official website: <http://kafka.apache.org/>

Kafka update records (to visually check if the project is still maintained): <https://kafka.apache.org/downloads>

#### RocketMQ

![](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/rocketmq-logo.png)

RocketMQ is an open-source "message, event, and stream" real-time data processing platform from Alibaba, which has drawn inspiration from Kafka and has become an Apache top-level project.

Core features of RocketMQ (from the RocketMQ official site):

- Cloud-Native: Designed for cloud environments with infinite scalability, Kubernetes-friendly.
- High Throughput: Guarantees trillions of throughput while meeting microservices and big data scenarios.
- Stream Processing: Provides lightweight, highly scalable, high-performance, and feature-rich stream computing engines.
- Financial Grade: Stable enough for enterprise-level core trading links.
- Simplified Architecture: No external dependencies, shared-nothing architecture.
- Ecosystem Friendly: Seamless integration with microservices, real-time computing, data lakes, and other surrounding ecosystems.

According to the official site,

> Since its inception, Apache RocketMQ has been widely adopted by numerous enterprise developers and cloud vendors due to its simple architecture, rich business functionality, and strong scalability. After ten years of large-scale scene refinement, RocketMQ has become a consensus choice for financial-grade reliable business messaging, widely used in e-commerce, big data, mobile internet, and IoT applications.

RocketMQ official site: <https://rocketmq.apache.org/> (the documentation is very detailed and highly recommended)

RocketMQ update records (to visually check if the project is still maintained): <https://github.com/apache/rocketmq/releases>

#### RabbitMQ

![](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/rabbitmq-logo.png)

RabbitMQ is a messaging middleware implemented in Erlang that uses AMQP (Advanced Message Queuing Protocol). It originally stemmed from financial systems for message storage and forwarding in distributed systems.

Today, RabbitMQ has gained recognition among many users due to its outstanding performance in usability, scalability, reliability, and high availability. The specific characteristics of RabbitMQ can be summarized as follows:

- **Reliability:** RabbitMQ employs mechanisms to ensure message reliability, such as persistence, delivery confirmations, and publishing confirmations.
- **Flexible Routing:** Messages are routed through exchangeers before entering queues. RabbitMQ has provided built-in exchangeers for typical routing functionalities. For more complex routing functionalities, multiple exchangeers can be bound together or custom exchangeers can be implemented via plugins, which will be detailed later in the core concepts of RabbitMQ.
- **Scalability:** Multiple RabbitMQ nodes can form a cluster and dynamically extend nodes based on actual business demands.
- **High Availability:** Queues can be mirrored across machines in the cluster, ensuring availability even if some nodes fail.
- **Support for Multiple Protocols:** In addition to natively supporting AMQP, RabbitMQ also supports STOMP, MQTT, and various other messaging middleware protocols.
- **Multi-language Clients:** RabbitMQ supports almost all commonly used languages such as Java, Python, Ruby, PHP, C#, and JavaScript.
- **User-Friendly Management Interface:** RabbitMQ provides an easy-to-use graphical interface for users to monitor and manage messages and nodes in the cluster. We will discuss this during the RabbitMQ installation.
- **Plugin Mechanism:** RabbitMQ offers numerous plugins for diverse functionalities and there is also the capability to develop custom plugins, somewhat akin to Dubbo's SPI mechanism.

RabbitMQ official site: <https://www.rabbitmq.com/>.

RabbitMQ update records (to visually check if the project is still maintained): <https://www.rabbitmq.com/news.html>

#### Pulsar

![](https://oss.javaguide.cn/github/javaguide/high-performance/message-queue/pulsar-logo.png)

Pulsar is a next-generation cloud-native distributed messaging streaming platform, initially developed by Yahoo, and has become an Apache top-level project.

Pulsar integrates messaging, storage, and lightweight function-based computing, adopting a compute-storage separation architecture design, and supports multi-tenancy, persistent storage, and cross-region data replication, offering strong consistency, high throughput, low latency, and high scalability characteristics for streaming data storage. It is considered one of the best solutions for real-time message transmission, storage, and computation in the cloud-native era.

Key features of Pulsar include (from the official site):

- A next-generation cloud-native distributed messaging streaming platform.
- A single instance of Pulsar natively supports multiple clusters and can seamlessly replicate messages across regions.
- Extremely low publish latency and end-to-end latency.
- Seamless scalability to over one million topics.
- A simple client API that supports Java, Go, Python, and C++.
- Various subscription modes for topics (exclusive, shared, and failover).
- A persistent message storage mechanism guaranteed by Apache BookKeeper.
- Stream native data processing realized by the lightweight serverless computing framework Pulsar Functions.
- A serverless connector framework based on Pulsar Functions called Pulsar IO makes it easier to move data into and out of Apache Pulsar.
- Layered storage allows data to be offloaded from hot storage to cold/long-term storage (such as S3, GCS) as it becomes stale.

Pulsar official site: <https://pulsar.apache.org/>

Pulsar update records (to visually check if the project is still maintained): <https://github.com/apache/pulsar/releases>

#### ActiveMQ

Currently deprecated; not recommended for use or learning.

### How to Choose?

> Refer to "Java Engineer Interview Quick Strike Season 1 - Teacher Zhonghua Shishan"

| Comparison Aspect | Overview                                                                                                                                                                                                                                                                                                             |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Throughput        | The throughput of ActiveMQ and RabbitMQ (with ActiveMQ being the worst performer) at tens of thousands is an order of magnitude lower than that of RocketMQ and Kafka at hundreds of thousands or even millions.                                                                                                     |
| Availability      | All can achieve high availability. ActiveMQ and RabbitMQ implement high availability based on a master-slave architecture, while RocketMQ is based on a distributed architecture. Kafka is also distributed with multiple data replicas, meaning that data loss is unlikely even if a small number of machines fail. |
| Timeliness        | RabbitMQ, being developed on Erlang, has strong concurrency capabilities and performs exceptionally well, achieving microsecond latency, while the others range in millisecond latency.                                                                                                                              |
| Feature Support   | Pulsar offers more comprehensive features, supporting multi-tenancy, various consumption modes, persistent modes, etc., and is the next-generation cloud-native distributed messaging stream platform.                                                                                                               |
| Message Loss      | The likelihood of message loss in ActiveMQ and RabbitMQ is very low; Kafka, RocketMQ, and Pulsar can theoretically achieve zero loss.                                                                                                                                                                                |

**Summary:**

- ActiveMQ has a mature community but is currently underperforming compared to others and experiences slow version iterations, leading to its deprecation.
- Although RabbitMQ's throughput lags behind Kafka, RocketMQ, and Pulsar, its concurrent capabilities are robustly supported by Erlang, leading to exceptional performance and low latency. However, few companies in China possess the capability to conduct Erlang-level code research and customizations. If your business scenario does not demand extremely high levels of concurrency (like hundreds of thousands or millions), RabbitMQ could be your first choice among these message queues.
- RocketMQ and Pulsar support strong consistency, making them suitable for scenarios demanding high message consistency.
- RocketMQ is produced by Alibaba, is an open-source project within the Java ecosystem, allowing one to read its source code directly and tailor it to company needs. RocketMQ has also undergone practical tests in Alibaba's business scenarios.
- Kafka's characteristics are notable: it focuses on providing fewer core functionalities but achieves extremely high throughput, ms-level latency, and exceptional availability and reliability, with flexible scalability. Kafka operates best with fewer topic counts to maintain its super high throughput. The only downside is the potential for message duplicates, which may have a minimal impact on data accuracy. However, this slight impact is negligible in the domain of big data and log collection. Kafka is indeed the industry standard for real-time computing and log collection in big data scenarios, with a highly active community that is unlikely to stagnate, making it the de facto norm worldwide in this field.

## References

- "Technical Architecture of Large-Scale Websites"
- KRaft: Apache Kafka Without ZooKeeper: <https://developer.confluent.io/learn/kraft/>
- What Are the Usage Scenarios for Message Queues?: <https://mp.weixin.qq.com/s/4V1jI6RylJr7Jr9JsQe73A>

<!-- @include: @article-footer.snippet.md -->
