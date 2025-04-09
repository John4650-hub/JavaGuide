I don't...
title: Timeout & Retry\
Category: High availability\
icon: retry\
I don't...

Because of the uncertainties of network problems, systems, or internal services, such as bugs, server failures, and the breakdown of operating systems, it is never possible for our systems or services to ensure that they are available at all times.

In order to minimize the effects of a system or service failure, we need **Timeout** and **Retry** mechanisms.

It is very simple to make the time-out and retry mechanisms clear because they are not deep concepts in themselves.

While the idea of time-out and retry mechanisms is simple, they are really very practical. The vast majority of the systems or services that you normally access involve remote calls that apply time-out and retry mechanisms. In particular, it is important for microservices systems to be properly timed and retried. Single-body services usually involve only network calls for databases, caches, third-party APIs, intermediates, etc., while there are network calls between services within the microservices system.

## Timeout mechanism

### What's a time-out mechanism?

The time-out mechanism states that when a request has not been processed for more than the specified time (e.g., 1s), the request is simply canceled and the designated anomaly or error is dropped (e.g., `504 Gateway Timeout`).

The timeout that we normally encounter can be divided into the following two:

- **Connect Timeout**: The longest waiting time for a client to connect to the service.
- **Read Timeout**: The client and the service have been connected, and the client is waiting for the service to process the request for the maximum time. In practical terms, we are concerned with more reading time out.

There may also be time lags in some connection-pool client frames to get connections over time and free connections.

If no timeout is set, there may be problems with service-end connection explosions and a large volume of requests piled up.

These piles of connections and requests consume system resources and affect the processing of newly received requests. In serious cases, the system or service may even be brought down.

I have experienced similar problems with an actual project before; the entire website was not able to process requests properly, and the server load was being filled directly. Later, it was discovered that the project timeout error, coupled with an anomaly in the processing of client requests, had resulted in service-end connections coming directly close to 400,000+, and that so many piles of connections had dried up the system.

### How should we set the timeout?

It's a hard question how long it should be set! There is a risk that the timeout is too high or too low. If it's too high, it reduces the effectiveness of the time-out mechanism; for example, if you set the time-out at 10s, it doesn't make much sense to set the time-out, and the system may still have a lot of slow request piles. If the setup is too low, it may lead to problems with the system or service being brought down in some cases where the processing of requests slows down (e.g., when requests suddenly increase), with a large number of requests retrying (often combined with retesting over time).

Normally, we suggest setting the read timeout at **1,500 ms**, which is a more general value. If your system or service is more sensitive to delay, read timeout values can be appropriately reduced to **1,500 ms**. On the other hand, the read timeout value can also be extended based on **1,500 ms**, but as far as possible, not to exceed **1,500 ms**. The connection can be appropriately long and is suggested to be within **1,000 ms ~ 5,000 ms**.

No silver bullets! The time-out value is specific to the size of the setting, or it needs to be optimized slowly in light of the needs and circumstances of the actual project.

To go further, by reference to the concept of the [moment's Java linear pool dynamic configuration](https://tech.meituan.com-2020/04/02/java-pooling-pretty-in-meituan.html), we can also make the timeout more configurable than fixed; the simplest way is to put the timeout value in the configuration center. In that case, we can adjust the timeout value to the state of the system or service.

## Try again

### What's a retry mechanism?

The retry mechanism is generally used in conjunction with the time-out mechanism and refers to multiple requests to avoid instantaneous and accidental failures.

An instantaneous failure can simply be understood as an accidental failure of an instantaneous system and is not sustainable. Occasional failures can be understood as occasional failures in some cases, usually with a lower frequency.

The idea at the core of the retry is that requests are handled successfully by depleting the resources of the server to obtain as much chance as possible. Since instantaneous and accidental malfunctions are rare, retrying to server resource consumption is almost negligible.

### What are the common retry strategies?

There are two common retry strategies:

1
