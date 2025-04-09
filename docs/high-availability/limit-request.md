I don't...
type: service limit details\
Category: High availability\
icon: limit_rate\
I don't...

In the case of software systems, the limit is to limit the speed of the request and to avoid the instant mass of requests that could destroy the software system. After all, the processing capacity of the software system is limited. If it exceeds its processing capacity, the software system may simply hang up.

Limited flow may result in users' requests not being processed correctly or immediately, although this is often the best solution after weighing the stability of the software system.

In real life, there is a limited flow of practical applications, such as queuing tickets to avoid a large influx of users, making them unmanageable.

# What are the common limit flow algorithms?

Brief introduction, four very understandable and easy to achieve limit algorithms!

> Picture from an article in InfoQ [Distributional Service Limiting Flows, which have been prepared for you](https://www.infoq.cn/article/Qg2tX8fyw5Vt-f3HH673).

## Fixed window counter algorithm

Fixed windows are, in fact, time windows. The rationale is to divide time into windows of fixed sizes, within each window to limit the number or speed of requests, i.e., the fixed window counter algorithm specifies the number of requests processed in a unit of the system.

If we set an interface in the system that can only be accessed 33 times a minute, the idea of using a fixed window counter is as follows:

- Split the time into a fixed-size window; here is a one-minute window.
- A variable `counter` is given to record the number of requests processed for the current interface, with an initial value of 0 (the request has not been processed within 1 minute of the current representative interface).
- `counter + 1` will be denied in all cases after each request has been processed within one minute, when `counter = 33` (i.e., the interface has been visited 33 times within that minute).
- At the end of 1 minute, replace `counter` with 0 and start counting again.

![fixed window counter algorithm](https://static001.infoq.cn/resource/image/8d/15/8ded7a2b90e144893f92ffffff55555b3615.png)

Advantages: Simple and easy to understand.

Disadvantages:

- Not smooth enough. For example, we limit an interface to only 30 visits per minute; assuming that 30 requests will arrive in the first 30 seconds, then 30 seconds will not be able to process the request, which is undesirable, and the user experience is extremely poor!
- There is no guaranteed limit rate to cope with sudden surges in traffic. For example, we limit an interface to only 1,000 visits in one minute, with 500 QPS for the interface. Before 55 seconds, one request for this interface was not received, and then 1 second suddenly received 1,000 requests. In the current scenario, the 1,000 requests can't be handled in 1 second, and the system is destroyed by instant requests.

## Sliding window counter algorithm

The sliding window counter algorithm is based on an upgraded version of the fixed window counter algorithm, with a smaller particle size.

The optimization of the sliding window counter algorithm compared to the fixed window counter algorithm is as follows: **It divides the time by a percentage**.

For example, our interface is limited to 60 requests per minute, and we can divide one minute into 60 windows. Move every one second; each window can only process requests that are not greater than `60 (requests) / 60 (windows)`, and no other requests will be processed if the total number of requests in the current window exceeds the limit.

Obviously, **the more the sliding window is divided, the smoother the sliding window is, and the more accurate the flow-restricted statistics are.**

![Sliding window counter algorithm](https://stat001.infoq.cn/resource/image/ae/15/ae4d3cd14efb8dc7046d691c90264715.png)

Advantages:

- Compared to a fixed window algorithm, a sliding window counter algorithm can cope with sudden surges of traffic.
- A sliding window counter algorithm has a smaller particle size than a fixed window algorithm, which provides more precise limit-to-flow control.

Disadvantages:

- Similar to the fixed window counter algorithm, there is still a problem that the sliding window counter algorithm is not smooth enough.
- The sliding window counter algorithm is more complex to achieve and understand than the fixed window counter algorithm.

## Leak bucket algorithm

We can compare the actions of the request to the injection of the bucket, and the process of processing the request can be compared to the leak of the bucket. We flow into the bucket at any rate and out at a certain rate. When the water exceeds the bucket flow, it is discarded because the bucket capacity is constant and the overall rate is ensured.

If you want to achieve
