I don't...
title: Introduction to Performance Test
Category: High availability
icon: et-performance
I don't...

Performance tests are usually done by this job, so why do we need to develop this? Knowledge of indicators, classifications, and tools for performance testing will help us to write better performance programs. As a role in the development of this field, if you can test performance, you will be given a lot of credit for your curriculum vitae.

In addition to this article, which I will take into account my actual experience and what I have learned from testing here, I have learned from some excellent books that I hope will help you.

# Different Characters Watching Website Performance

## User

What is the most important concern when users open a website? Of course, the response rate is slow. For example, when we click on the homepage, how long does it take for the first page to appear in front of us? How long does it take to return the results by clicking on the order button?

So, users often judge your website's performance in terms of your speed of response when they experience our system.

## Developer

Users and developers are concerned about speed, which is actually the speed with which our system **processes user requests**.

It is generally difficult for developers to judge the performance of their websites in an intuitive manner, and we tend to give an approximate value based on the current structure and infrastructure of the sites, such as:

1. Is the project structure distributed?
1. Did you use the cache and message queue?
1. Are there special treatments for high-symmetry operations?
1. Is the design of the database reasonable?
1. Does the algorithm used by the system need to be optimized?
1. Is there a problem with memory leakage in the system?
1. How large is the Redis cache used by the project? How's the server working? Is it a mechanical hard drive or a solid-state drive?
1. ...

## Tester

Testers generally test on the basis of performance test tools and then produce a table. This table may cover these important elements:

1. Response time;
1. Success rate of requests;
1. Throughput;
1. ...

## Transport People

Transporters tend to judge the performance of the website based on the use of infrastructure and resources, such as the rational use of our server resources, whether there is any misuse of database resources, and, of course, whether it is a traditional provider. Now that DevOps is on fire, there is little purely dry. We retain this role here for the time being.

# The Point Where Performance Tests Need Attention

Few articles refer to this in the context of performance tests, and everyone talks about how to conduct performance tests and what performance test indicators are.

# Know the Business Scene of the System

**You need to know the business scene of the current system before the performance test.** The lack of in-depth knowledge of the system's operations makes it easy for us to make errors in the direction of testing, which leads us to ignore certain areas of the system where performance testing is more needed. For example, our system can provide users with emails, which can be sent only after a successful email has been configured and the corresponding mailbox has been entered, and the system can handle about 10,000 requests per day. A lot of people may begin to test the email delivery interface directly, but emailing may not be a performance bottleneck for the current system, so many people are using our system to email, and there may be a lot of emails.

## Historical Data Are Very Useful

The historical data left by the current system are very important, and in general, we can make a preliminary determination of which interfaces to the system are more used and which services are under the greatest pressure, so that we can conduct more detailed performance testing and analysis of these areas.

And they're just like a slab in the system, and they're perfected to bring a qualitative upgrade to our system.

# Common Performance Indicators

## Response Time

**Response Time (RT) is the time required for the user to send a request to receive the results of the system processing.**

RT is a very important and intuitive indicator, and the RT value directly reflects the speed with which the system processes user requests.

## Concurrent Users

**The concurrent users can be simply understood as the number of requests for which the system can be accessed at the same time, which means that the system can process multiple requests simultaneously.**

The concurrent number reflects the load capacity of the system.

## QPS and TPS

- **QPS (Queries Per Second)**: number of queries per second for the server;
- **TPS (Transactions Per Second)**: number of services per second for the server (a service here is understood to be the process of sending a request to the server);

The book describes the difference between QPS and TPS.

> QPS vs TPS: QPS is more or less similar to TPS, but the difference is that one visit to a page leads to a TPS; however, a single page request may result
