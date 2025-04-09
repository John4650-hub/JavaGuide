I don't...
Title: RocketMQ Summary of Common Issues
Category: High Performance
Tag:

- RocketMQ
- Message Queue
  I don't...

> [Text by FrancisQ](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA=&mid=2247485969&idx=1&sn=6bd53abde30d42a778d5a35ec4428c&chksm=cea245daf9d5ccccce6331f93115f0c2c4a7634e55f5f5f9009fd03f5a5a55b7545b5ef4f4f0530&token=294077121&lang=zh_CN#rd) These improvements were mainly made in the original language:
>
> - [Analysis of the Causes and the Specifics of Sequenced Consumption of RocketMQ High-Performance Reading and Writing](https://github.com/Snailclimb/JavaGuide/pull/2133)
> - [The Type of Message, Type of Consumer, Consumer and Producer Groups Were Added](https://github.com/Snailclimb/JavaGuide/pull/2134)

# Message Line Literacy

I'm not going to explain the line. Don't tell me you don't even know what the line is.

So the question is not what's in the news queue, but what's in the news queue? What's the line for? What good would it do? Does the news queue have side effects?

# What's Going on in the News Team?

The news queue is a necessary skill for a back-end programmer, as **the distributed application must involve communication between systems** and the news queue should also emerge at this time. It can be said that distributional production is the basis of news queues, and distributional fear is an old concept, so it is also an old medium.

What's the message line for?

# It's a Walk

You might argue that the communication between applications is not just a line of information. Why does a good line of information have to be in the middle? Can't I communicate directly?

Well, you're proposing another concept, **Sync Communication.** For example, `Dubbo` is an `RPC` framework for synchronized communication between systems, which is now used more by the industry.

Let me pick up a little bit, like we have a ticket-buying system, and the demand is that the user receives the completed text after the purchase.

![Ticket Buying System](https://oss.javaguide.cn/github/javaguide/high-perform/message-Que/16ef37ee7e09230.jpg)

We omit the average network communication time consumption, and if the ticketing system requires 150 ms and the text message system requires 200 ms, then the whole process is 150 ms + 200 ms = 350 ms.

Of course, there's nothing wrong at first sight. But when you think about it, it's a bit problematic that I actually bought tickets when I was in the ticket-purchase system, and I'm now synchronizing the whole request for a long time, and the SMS thing is not really necessary, it's just a helper to enhance user experience. I'm feeling a little bit like a top-heavy now, and buying tickets is a less time-consuming process, and now I have to wait for the more time-consuming operation of texting. What if I added another e-mail?

![Email Notification](https://oss.javaguide.cn/github/javaguide/high-perform/message-queue/16ef380429cf373e.jpg)

So the whole system's call chain becomes longer and the whole time becomes 550 ms.

When we need to line up in the canteens in our student age, we are a synchronized model with the canteen aunt.

We need to tell the chef: "Sister, put me a chicken leg, put me a sour potato, pour me some juice, make me some more food."

And then she helped us cook and cook, and we watched her shaking hands and falling potatoes swallow.

Eventually, we picked up our meals from our aunt and went looking for a seat...

In retrospect, we're **waiting for our mom to cook for me,** and we're just adding chicken legs and potatoes, and if I add ketchup and an egg, it's gonna be longer, and we're waiting for it.

And then, when we got paid to go to the hotel, we told the waiter to have a bowl of beef noodles with an egg **(transmitting a message)** and then we could play with the phone on the table **(do something else)** and we could eat it when we got our beef noodles. And then we sent a message, and then we turned to other things. It's not as short as that, but we just have to send one message to do something else, it's a concept of **a different step**.

So, in order to solve this problem, intelligent programmers have added to the middle a message line similar to that of the waiter. And
