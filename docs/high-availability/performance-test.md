---
title: Introduction to Performance Testing
category: High Availability
icon: et-performance
---

Performance testing is generally conducted by testers, so why should developers learn about it? Understanding performance testing metrics, classifications, and tools helps us write better-performing programs. Furthermore, if you as a developer possess performance testing skills, it will undoubtedly enhance your resume.

This article combines my personal experiences and insights from testing, along with references to some excellent books, hoping to be of help to you.

## Perspectives on Website Performance from Different Roles

### Users

When a user opens a website, what do they pay the most attention to? Certainly, it is the website's response speed. For instance, when we click on the homepage of Taobao, how long does it take for Taobao to display the content? How long does it take to return a result after clicking the "submit order" button, etc.

Therefore, when users experience our system, they often judge the performance of your website based on its response speed.

### Developers

Both users and developers focus on speed, which truly refers to our system's **processing speed of user requests**.

Developers generally find it challenging to intuitively assess the performance of their website. We often provide an approximate value based on the current architecture and infrastructure of the website, for instance:

1. Is the project architecture distributed?
1. Are caching and messaging queues utilized?
1. Are there any special handling for high-concurrency business?
1. Is the database design reasonable?
1. Do the algorithms used in the system need optimization?
1. Does the system have memory leak issues?
1. How large is the Redis cache used in the project? What is the server performance like? Is it using a mechanical hard disk or a solid-state drive?
1. ……

### Testers

Testers typically conduct tests according to performance testing tools and usually create a table. This table might cover important contents like:

1. Response time;
1. Request success rate;
1. Throughput;
1. ……

### Operations Personnel

Operations personnel tend to evaluate the website's performance based on infrastructure and resource utilization rates. For instance, they might analyze whether resource usage on our servers is reasonable or whether there is abuse of database resources. Of course, this is the traditional role of operations personnel. With the rise of DevOps, pure operations roles are less common now, although we still acknowledge this role for the time being.

## Key Points to Note in Performance Testing

Few articles address this issue when discussing performance testing. Most articles focus on how to conduct performance tests and what performance testing metrics exist.

### Understanding the Business Context of the System

**Before performance testing, it is crucial to comprehend the business context of the current system.** If our understanding of the system's business is not deep enough, we easily make biased testing direction mistakes, leading us to overlook certain areas of the system that need performance testing. For example, if our system provides users with the ability to send emails, after setting up a successful email address, users can simply input the corresponding email to send, and the system can handle thousands of email-sending requests daily. Many would immediately begin using relevant tools to test the email-sending interface, but sending emails may not currently be the performance bottleneck of the system. With so many users sending emails, there might be many others sending emails simultaneously, so user management could be the actual performance bottleneck!

### Historical Data is Very Useful

The historical data left by the current system is very important. Generally, we can use relevant historical data to initially determine which interfaces are being called frequently and which services are under the most pressure. This allows us to conduct more detailed performance testing and analysis targeted at these areas.

Moreover, these areas are like weak points in the system; optimizing them can result in a significant qualitative improvement for our system.

## Common Performance Metrics

### Response Time

**Response Time RT (Response-time) is the time taken from when a user issues a request to when the user receives the system's processed result.**

RT is a very important and intuitive metric. The size of the RT value directly reflects how quickly the system processes user requests.

### Concurrent Users

**Concurrent users can be simply understood as the number of people the system can support to access and use simultaneously, meaning the number of requests the system can handle at the same time.**

The number of concurrent users reflects the system's load capacity.

### QPS and TPS

- **QPS (Queries Per Second)**: The number of queries the server can execute per second;
- **TPS (Transactions Per Second)**: The number of transactions processed by the server per second (a transaction can be understood as the process from when a customer issues a request to when they receive a response from the server).

The book describes the difference between QPS and TPS as follows:

> QPS vs TPS: QPS is quite similar to TPS, but the difference lies in that one access to a page counts as one TPS; however, a single page request may generate multiple requests to the server, which can be counted as "QPS." For example, if accessing a page generates 2 requests to the server, one access would result in one "T" and two "Q".

### Throughput

**Throughput refers to the number of requests processed by the system within a unit of time.**

The throughput of a system is closely related to the resource consumption of requests. The more resources a request consumes from the system, the lower the system's throughput capability, and vice versa.

TPS and QPS are commonly used quantitative metrics for throughput.

- **QPS (TPS)** = Concurrent Users / Average Response Time (RT)
- **Concurrent Users** = QPS * Average Response Time (RT)

## System Activity Indicators

### PV (Page View)

Page Views refer to website traffic or click counts, measuring the number of pages visited by users; each time a user opens or refreshes a page within a specific statistical period, it records one instance; multiple openings or refreshes of the same page accumulate the view count. UV is counted from the perspective of the number of pages opened/refreshed.

### UV (Unique Visitor)

Unique Visitors refer to the count of users visiting a site within one day. If the same visitor accesses the site multiple times in one day, they are only counted as one unique visitor. UV is counted from the perspective of individual users.

### DAU (Daily Active Users)

The number of daily active users.

### MAU (Monthly Active Users)

The number of monthly active users.

For example, if a website has a DAU of 12 million, with an average usage time of 1 hour per day, and an RT of 0.5s, we can calculate the concurrent user load and QPS.

Average concurrent users = DAU (12 million) * Average usage time (1 hour, 3600 seconds) / Seconds in a day (86400) = 12 million / 24 = 500,000

Real concurrent users (considering lower usage during certain periods) = DAU (12 million) * Average usage time (1 hour, 3600 seconds) / Seconds in a day - assume 8 hours (57600) for low access time = 12 million / 16 = 750,000

Peak concurrent users = Average concurrent users * 6 = 3 million

QPS = Real concurrent users / RT = 750,000 / 0.5 = 1.5 million/s

## Classification of Performance Testing

### Performance Testing

Performance testing methods simulate user requests to the system using testing tools, primarily to verify whether the system's performance meets the requirements. Simply put, this method aims to validate the system's capacity under specific operating conditions.

Performance testing is carried out after you have some understanding of system performance, with clear performance metrics.

### Load Testing

Continuously increasing request pressure on the tested system until some resource on the server reaches saturation, such as when system caching is insufficient or the response time is no longer acceptable.

In simpler terms, load testing tests the limits of the system.

### Stress Testing

Pressuring the system with requests regardless of resource utilization until the server crashes and can no longer provide services.

### Stability Testing

Simulating real scenarios by applying a certain amount of pressure on the system to see if the business can operate stably.

## Commonly Used Performance Testing Tools

### Common Backend Tools

Since system design involves performance-related issues, interviewers are likely to ask, **How do you conduct performance testing?**

Here are four commonly used performance testing tools:

1. **JMeter**: Apache JMeter is a performance testing tool developed in Java.
1. **LoadRunner**: A commercial performance testing tool.
1. **Gatling**: A high-performance server performance testing tool developed in Scala.
1. **ab**: Full name is Apache Bench. It is a testing tool under the Apache umbrella and is very practical.

If I remember correctly, all the performance testing tools mentioned, except for **LoadRunner**, are open-source and free.

### Common Frontend Tools

1. **Fiddler**: A packet capturing tool that can modify request data, and even server response data, with powerful features; it's an essential tool for web debugging.
1. **HttpWatch**: A tool for recording HTTP request information.

## Common Performance Optimization Strategies

Before performance optimization, we need to analyze each stage of the requests to identify potential performance bottlenecks and locate issues.

Here are some questions I often ask myself during performance optimization:

1. Does the system require caching?
1. Does the system architecture itself have problems?
1. Are there deadlock situations in the system?
1. Does the system have memory leaks? (While Java’s automatic memory recovery is very convenient, poorly written code can sometimes lead to memory leaks.)
1. Is the database indexing reasonable?
1. ……

<!-- @include: @article-footer.snippet.md -->
