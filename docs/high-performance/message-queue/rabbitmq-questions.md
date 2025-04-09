I don't...
Title: RabbitMQ Summary of Common Issues
Category: High Performance
Tag:

- Message Queue
  Head:
- Meta
- Name: Keywords
  Contact: RabbitMQ, AMQP, Broker, Exchange, Priority Line, Delay Line
- Meta
- Name: Description
  Content: RabbitMQ is a reusable business message system based on AMQP. It can be used for efficient communication between the various modules of a large software system, to support high co-production and to support expansion. It supports a wide range of clients such as Python, Ruby, .NET, Java, JMS, C, PHP, ActionScript, XMPP, STOMP, etc., and AJAX, which is sustainable and is used to store forward messages in distributed systems and perform well in terms of ease of use, extension, high availability, etc.
  I don't...

> This article was collected by JavaGuide from the Internet and is unknown.

# RabbitMQ: What Is It?

RabbitMQ is a reusable business message system based on AMQP. It can be used for efficient communication between the various modules of a large software system, to support high co-production and to support expansion. It supports a variety of clients such as Python, Ruby, .NET, Java, JMS, C, PHP, ActionScript, XMPP, STOMP, etc., and supports AJAX, which lasts forever and is used to store forwarding messages in distributed systems, which perform well in terms of ease of use, extension, high availability, etc.

RabbitMQ is an open-source message queue that uses Erlang, which itself supports a number of protocols: AMQP, XMPP, SMTP, STOMP, making it very heavyweight and more suitable for enterprise-level development. It also achieves a broker structure, which means that messages are queued at the center when they are sent to the client, with good support for routers, load balancers, or data permanence.

PS: What's the message queue? A message queue is a component that uses queues to communicate.

# RabbitMQ Characteristics

- **Reliability**: RabbitMQ uses a number of mechanisms to ensure reliability, such as durability, transmission confirmation, and issuance of confirmations.
- **Flexible Routing**: Pass the message through an exchange before the message goes in line. For typical routing functions, RabbitMQ has provided some built-in exchanges. For more complex routing functions, you can bind multiple exchanges together, or you can use plug-in mechanisms to create your own exchanges.
- **Extension**: Multiple RabbitMQ nodes can form a cluster, or they can be expanded dynamically according to the actual business situation.
- **High Usability**: Queues can set a mirror on a machine in a cluster so that the queue is still available in case of problems at some nodes.
- **Multiple Protocols**: RabbitMQ supports multiple messaging protocols such as STOMP, MQTT, in addition to the original AMQP protocols.
- **Multilingual Client**: RabbitMQ supports almost all commonly used languages such as Java, Python, Ruby, PHP, C#, JavaScript, etc.
- **Management Interface**: RabbitMQ provides an easy-to-use user interface that allows users to monitor and manage messages, nodes in clusters, etc.
- **Plugin Mechanism**: RabbitMQ provides many plugins to achieve multi-faceted expansion and, of course, to create its own plugins.

# RabbitMQ Core Concept

RabbitMQ is a producer-consumer model that is primarily responsible for receiving, storing, and transmitting information. You can imagine that when you send a package to the post office, the post office will hold it up and eventually send the mail through the postman to the recipient's hand. RabbitMQ is like a system of post offices, mailboxes, and mailmen. In terms of computer terminology, the RabbitMQ model is more like a switchboard model.

The overall model structure of RabbitMQ is as follows:

![RabbitMQ Model Structure](https://oss.javaguide.cn/github/javaguide/rabbitmq/96388546.jpg)

I will then introduce some of the concepts in the above figure.

## Producer (Producer) and Consumer (Consumer)

- **Producer**: The party producing the message (mail sender).
- **Consumer**: The party consuming the message (mail recipient).

Messages generally consist of two parts: **headers** (or labels) and **messages**. Messages can also be called payloads, which are non-transparent, while the headers consist of a series of optional attributes, such as routing-key (routing key), priority (priority over other messages), and delivery-mode (specifying that the message may require persistent storage). When producers send the information to RabbitMQ, RabbitMQ sends it to interested consumers based on this information.

## Exchange

In RabbitMQ, the message is not delivered directly to the **Queue**, but must go through the **Exchange** to the corresponding **Queue**.

**Exchange** is used to receive information from producers and route it to the server. If it is not available, it may return to the **Producer** and
