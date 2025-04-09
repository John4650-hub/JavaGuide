I don't...
Title: Message Queue Basics Summary
Category: High performance
Tag:

- Message queue.
  I don't...

::: Tip

The news queue in this article refers mainly to distributed news queues.

:::

“RabbitMQ?” “Kafka?” “RocketMQ?” ... We often hear the word queuing in daily learning and development. I have also mentioned this concept in several of my articles. Maybe you're an expert in the use of the news queue, or you're new to the news queue, and whether you don't know the news queue, this paper will lead you to some of the basic theories of the news queue.

If you're an expert, you might learn from this paper some important ideas about the news queue that you didn't notice before, and if you're a rookie, believe this will be a brick that you opened the news queue.

# What's the news queue?

We can view news queues as a container for information, and when we need to use it, we can just get it out of the container for our own use. Since a queue is an advanced, first-in-first-out data structure, messages are also consumed sequentially.

![Image](https://oss.javaguide.cn/github/javaguide/high-perform/message-queue/message-queue-small.png)

The two parties involved in the transmission of the message are known as **Producer** and **Consumer**, and the producer is responsible for sending the message and the consumer is responsible for handling it.

![Publication/subscription (Pub/Sub) model](https://oss.javaguide.cn/github/javaguide/high-perform/message-queue/message-queue-pub-sub-model.png)

An important way to communicate processes in the operating system is by queuing messages. The line of information that we refer to here is slightly different and refers more to communications between the various services and components/modules within the system, which is one **medium**.

Wikipedia presents the medium:

> Middleware (English: Middleware), which translates to intermediate, intermediate level, is a type of software that provides connectivity between systems and applications and facilitates communication between components of the software, which allows information and resources to be shared between different technological structures through intermediates. The middleware is located above the operating system of the client-server and manages computing resources and network communications.

In simple terms: **Middleware is a type of software that serves applications that are for users who do not have access to or use the middleware.**

In addition to the news queue, common middleware includes the RPC framework, distributed components, HTTP servers, task control framework, configuration center, database-level partitioning, and data migration tools.

For a more detailed description of the middleware, reference can be made to an answer to the Alibaba technique: <https://www.zhihu.com/question/19730582/answer/1663627873>.

With the development of distributed and microservices systems, there is greater scope for the news queues to be included in the design of the system, and the use of news queues can reduce the coupling of the system, achieve mission decoupling, and effectively peak the flow, which is one of the important components of distributed and microservice systems.

What's on the news queue?

Usually, the use of news queues can bring three main benefits to our system:

1. Overstepping
1. Peak/restricted flow
1. Reducing system coupling

In addition to these three points, there are other applications in the news queue, such as achieving distributed services, sequencing, and data flow processing.

If you are asked this question by the interviewer during the interview, the general situation is that you are involved in the news queue on your curriculum vitae, and at this point, you are recommended to answer it in the context of your own project.

# By foot

![Improving systemic performance through walk-through](https://oss.javaguide.cn/github/javaguide/Asynchronous-message-queue.png)

The time-consuming operation contained in the user request is done by moving the message to a different step, returning the corresponding message as soon as it is sent to the message queue, reducing response time and enhancing user experience. The system then consumes the message.

Because the user requested that the data be written in the message queue and returned to the user, the requested data may fail in subsequent operations such as checking, writing, etc. As a result, **after a different process using a message queue, there is a need for appropriate business process modification and alignment**, for example, after an order has been submitted by the user, the order data is written into the message queue, cannot be returned immediately to the successful submission of the order, and it is necessary to notify the user of the successful order by e-mail or text message after the customer process in the message queue has actually processed the order, or even after the library has been discharged, in order to avoid a transaction dispute. It's like we book trains and movies on our phones.

# Peak/restrict

\*\*The short, high, and generated transactional messages are stored in the message queue, then the back-end service slowly consumes them according
