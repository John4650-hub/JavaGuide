---
title: Summary of Common RabbitMQ Issues
category: High Performance
tag:
  - Message Queue
head:
  -   - meta
      - name: keywords
        content: RabbitMQ, AMQP, Broker, Exchange, Priority Queue, Delay Queue
  -   - meta
      - name: description
        content: RabbitMQ is a reusable enterprise messaging system built on the Advanced Message Queuing Protocol (AMQP). It can be used for efficient communication between various modules of large software systems, supports high concurrency, and is scalable. It supports multiple clients such as Python, Ruby, .NET, Java, JMS, C, PHP, ActionScript, XMPP, STOMP, etc., supports AJAX, persistence, and is used for storing and forwarding messages in distributed systems, performing well in usability, scalability, and high availability.
---

> This article is collected from the internet by JavaGuide, original source unknown.

## What is RabbitMQ?

RabbitMQ is a reusable enterprise messaging system built on the Advanced Message Queuing Protocol (AMQP). It can be used for efficient communication between various modules of large software systems, supports high concurrency, and is scalable. It supports multiple clients such as Python, Ruby, .NET, Java, JMS, C, PHP, ActionScript, XMPP, STOMP, etc., supports AJAX, persistence, and is used for storing and forwarding messages in distributed systems, performing well in usability, scalability, and high availability.

RabbitMQ is an open-source message queue written in Erlang, supporting many protocols: AMQP, XMPP, SMTP, STOMP. This makes it very heavyweight and more suitable for enterprise-level development. It also implements a Broker architecture, which means that messages are queued in a central queue before being sent to clients, providing good support for routing, load balancing, and data persistence.

PS: You may also directly ask what a message queue is? A message queue is a component that uses a queue for communication.

## What are the features of RabbitMQ?

- **Reliability**: RabbitMQ uses mechanisms to ensure reliability, such as persistence, delivery confirmations, and publish confirmations.
- **Flexible Routing**: Messages are routed through an exchange before entering the queue. RabbitMQ provides several built-in exchanges for typical routing functions. For more complex routing, multiple exchanges can be bound together, or custom exchanges can be implemented through a plugin mechanism.
- **Scalability**: Multiple RabbitMQ nodes can form a cluster and can dynamically expand nodes in the cluster based on actual business needs.
- **High Availability**: Queues can be mirrored across machines in the cluster, ensuring that queues remain available even if some nodes encounter issues.
- **Multiple Protocols**: In addition to natively supporting the AMQP protocol, RabbitMQ also supports various middleware protocols such as STOMP and MQTT.
- **Multi-language Clients**: RabbitMQ supports almost all commonly used languages, such as Java, Python, Ruby, PHP, C#, JavaScript, etc.
- **Management Interface**: RabbitMQ provides an easy-to-use user interface for monitoring and managing messages, nodes in the cluster, etc.
- **Plugin Mechanism**: RabbitMQ offers many plugins for various extensions, and users can also write their own plugins.

## What are the core concepts of RabbitMQ?

Overall, RabbitMQ is a producer-consumer model responsible for receiving, storing, and forwarding messages. You can imagine the message delivery process as follows: when you send a package to the post office, the post office temporarily stores it and eventually delivers it to the recipient through a mail carrier. RabbitMQ is like a system composed of a post office, mailboxes, and mail carriers. From a computer terminology perspective, the RabbitMQ model resembles a switch model.

The overall model architecture of RabbitMQ is as follows:

![Figure 1 - Overall Model Architecture of RabbitMQ](https://oss.javaguide.cn/github/javaguide/rabbitmq/96388546.jpg)

Below, I will introduce some concepts from the above diagram one by one.

### Producer and Consumer

- **Producer**: The party that produces messages (mail sender).
- **Consumer**: The party that consumes messages (mail recipient).

Messages generally consist of two parts: **message header** (or label) and **message body**. The message body can also be called payload; it is opaque, while the message header consists of a series of optional attributes, including routing-key, priority (relative to other messages), delivery-mode (indicating whether the message may require persistent storage), etc. After the producer hands the message to RabbitMQ, RabbitMQ sends the message to interested consumers based on the message header.

### Exchange

In RabbitMQ, messages are not delivered directly to the **Queue**; they must first pass through the **Exchange**, which assigns our messages to the corresponding **Queue**.

The **Exchange** receives messages sent by producers and routes them to queues in the server. If routing fails, the message may be returned to the **Producer** or discarded
