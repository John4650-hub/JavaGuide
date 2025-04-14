---
title: How Programmers Can Quickly Learn New Technologies
category: Selected Technical Articles
tag:
  - Leveling Up Strategy
---

> **Recommendation**: This is an article from the leveling up strategy section of [《Java Interview Guide》](https://javaguide.cn/zhuanlan/java-mian-shi-zhi-bei.html), sharing my views on how to quickly learn a new technology.
>
> ![《Java Interview Guide》 leveling up strategy section](https://oss.javaguide.cn/javamianshizhibei/training-strategy-articles.png)

Often, we need to quickly learn a technology due to work reasons and then apply it in projects. Alternatively, we might need to learn a technology that the company we are interviewing with requires, which we have not encountered before, in order to prepare for the interview.

As a self-taught programmer, this article briefly discusses my thoughts on how to quickly learn a technology.

When learning any technology, it's essential to first understand what problem this technology aims to solve. Before diving deeper into the technology, start by gaining a global perspective of it, considering what modules it consists of, what features it provides, and what advantages it has compared to similar technologies.

For instance, when we learn Spring, you can know about the latest technological developments of Spring and what modules it includes through the official Spring documentation, as well as what problems Spring can help you solve.

![](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/20210506110341207.png)

Similarly, when I'm learning message queues, I would first understand the general role of this message queue in systems and what problems it helps us solve. There are many types of message queues, and when specifically studying a certain message queue, I would compare it with the ones I've learned before. For example, when learning RocketMQ, I would compare it with the first message queue I learned, ActiveMQ, considering what improvements RocketMQ has made over ActiveMQ, what pain points it addresses, what similarities exist between the two, and what differences there are.

**The most effective and quickest way to learn a technology is to establish connections between this technology and what you have learned before, forming a network.**

Then, I recommend you check out the official documentation tutorials, run related demos, and work on some small projects.

However, official documentation is often in English, and typically only domestic projects and a few foreign projects offer Chinese documentation. Moreover, the official documents are usually somewhat superficial and may not be well-suited for beginners as learning material.

If you find it hard to understand the official documents, you can search for related keywords to find some high-quality blogs or videos. **Do not start by trying to understand the principles of the technology right away.**

For instance, when learning the Spring framework, after understanding the problems Spring framework addresses, instead of diving directly into the principles or source code of Spring, it's better to first experience the core functionalities provided by the Spring framework, such as IoC (Inversion of Control) and AOP (Aspect-Oriented Programming). You can write some demos using the Spring framework or even undertake small projects using it.

In summary, **before studying the principles of a technology, you should first understand how to use that technology.**

This step-by-step learning process can gradually help you build a sense of enjoyment in learning and achieve immediate feelings of accomplishment, avoiding the discouragement that can come from directly tackling theoretical knowledge.

**When studying the principles of a technology, to avoid the content being too abstract, we can also practice hands-on.**

For example, when studying the principle of Tomcat, we might find that Tomcat's custom thread pool is quite interesting, prompting us to write our own customized thread pool. Similarly, when studying the principle of Dubbo, we could create a simplified version of an RPC framework.

Additionally, learning technologies needed for projects and those required for interviews does have some differences.

If you're learning a technology for actual project use, your focus should be on understanding how to utilize it and best practices, as well as being aware of potential issues that may arise during usage. Your ultimate goal is to achieve a tangible, positive effect from this technology in the project.

If you are only learning a technology for the sake of an interview, your focus should be on the most common questions regarding that technology in interviews, often referred to as "eight-legged essays."

Many people disdain the term "eight-legged essays." However, I believe that if you don't rote learn them but think about the essence of these interview questions, then preparing for them can also deepen your understanding of the technology.

Finally, the most important and challenging aspect is **unity of knowledge and action! Unity of knowledge and action! Unity of knowledge and action!** Whether in programming or other fields, what matters most is not how much you know but how well you can apply that knowledge in practice.
