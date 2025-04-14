---
title: Detailed Explanation of Load Balancing Principles and Algorithms
category: High Performance
head:
  - - meta
    - name: keywords
      content: Client-side Load Balancing, Server-side Load Balancing, Nginx, Load Balancing Algorithms, Layer 7 Load Balancing, DNS Resolution
  - - meta
    - name: description
      content: Load balancing refers to distributing user requests across different servers to improve the overall concurrency and reliability of the system. Load balancing can be simply divided into two types: server-side load balancing and client-side load balancing. Server-side load balancing involves more knowledge points and is encountered more frequently in work, thus, I will spend more time introducing it.
---

## What is Load Balancing?

**Load balancing** refers to distributing user requests across different servers to enhance the overall concurrency capacity and reliability of a system. Load balancing services can be implemented by specialized software or hardware. Generally, hardware has better performance, while software is cheaper (which will be discussed in detail later).

The following image is from an article in the "High Concurrency" section of [“Java Interview Guide”](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247519384&idx=1&sn=bc7e71af75350b755f04ca4178395b1a&chksm=cea1c353f9d64a458f797696d4144b4d6e58639371a4612b8e4d106d83a66d2289e7b2cd7431&token=660789642&lang=zh_CN&scene=21#wechat_redirect). From the image, we can see that the system's product service is deployed across multiple servers, and to achieve request sharding for accessing the product service, we utilize load balancing.

![Multiple Service Instances - Load Balancing](https://oss.javaguide.cn/github/javaguide/high-performance/load-balancing/multi-service-load-balancing.drawio.png)

Load balancing is a commonly used and relatively simple means of enhancing system concurrency and reliability. It is utilized in nearly all systems, whether they are monolithic architectures or microservice architectures.

## What Types of Load Balancing Are There?

Load balancing can be simply divided into **server-side load balancing** and **client-side load balancing**.

Server-side load balancing involves more knowledge points and is encountered more frequently in work, hence I will spend more time introducing it.

### Server-side Load Balancing

**Server-side load balancing** is mainly applied between **external system requests** and the **gateway layer** and can be implemented using **software** or **hardware**.

The following diagram illustrates a simple server-side load balancing setup based on Nginx:

![Server-side Load Balancing Based on Nginx](https://oss.javaguide.cn/github/javaguide/high-performance/load-balancing/server-load-balancing.png)

**Hardware load balancing** is achieved via specialized hardware devices (such as **F5, A10, Array**).

The advantage of hardware load balancing is its strong and stable performance; however, the downside is that it is quite expensive. For instance, a basic model of F5 starts from over 200,000 yuan, which most companies cannot afford. If the business volume is not high, it is unnecessary to invest in hardware for load balancing; software load balancing suffices!

In our daily development, we generally have limited exposure to hardware load balancing, as we encounter **software load balancing** more frequently. Software load balancing uses software (such as **LVS, Nginx, HAProxy**) to implement load balancing functionality; while its performance is slightly lower, it is more cost-effective! For instance, a basic Linux server can be just a few thousand yuan, and a slightly better one might cost around 20,000 to 30,000 yuan.

According to the OSI model, server-side load balancing can also be categorized into:

- Layer 2 Load Balancing
- Layer 3 Load Balancing
- Layer 4 Load Balancing
- Layer 7 Load Balancing

The most common types are Layer 4 and Layer 7 load balancing, thus this article will primarily focus on these two types.

> The Nginx official website provides a detailed introduction to Layer 4 and Layer 7 load balancing. If you're interested, you can check it out.
>
> - [What Is Layer 4 Load Balancing?](https://www.nginx.com/resources/glossary/layer-4-load-balancing/)
> - [What Is Layer 7 Load Balancing?](https://www.nginx.com/resources/glossary/layer-7-load-balancing/)

![OSI Seven Layer Model](https://oss.javaguide.cn/github/javaguide/cs-basics/network/osi-7-model.png)

- **Layer 4 load balancing** operates at the fourth layer of the OSI model, which is the transport layer. The main protocols at this layer are TCP/UDP. The load balancer at this layer can see the source and destination port addresses in the data packets and will forward the packets to the backend real servers based on this information using specific load balancing algorithms. In other words, the core of Layer 4 load balancing is IP + port level load balancing without involving the actual content of the messages.
- **Layer 7 load balancing** functions at the seventh layer of the OSI model, which is the application layer. The main protocol at this layer is HTTP. The load balancing at this layer is more complex than at Layer 4, as it reads the message data section (for instance, the HTTP portion of messages), and makes load balancing decisions based on the content read (such as URL, Cookie). In other words, the core of Layer 7 load balancers is load balancing at the message content level (such as URL, Cookie), and devices performing Layer 7 load balancing are usually referred to as **reverse proxy servers**.

Layer 7 load balancing consumes more performance than Layer 4 load balancing, but it is also relatively more flexible and capable of routing network requests more intelligently. For example, you can optimize based on request content, such as caching, compression, and encryption.

In short, **Layer 4 load balancing has strong performance, while Layer 7 load balancing has more powerful functionalities!** However, for most business scenarios, the performance difference between Layer 4 and Layer 7 load balancing can generally be negligible.

The following excerpt is from the Nginx official article [What Is Layer 4 Load Balancing?](https://www.nginx.com/resources/glossary/layer-4-load-balancing/).

> Layer 4 load balancing was a popular architectural approach to traffic handling when commodity hardware was not as powerful as it is now, and the interaction between clients and application servers was much less complex. It requires less computation than more sophisticated load balancing methods (such as Layer 7), but CPU and memory are now sufficiently fast and cheap that the performance advantage for Layer 4 load balancing has become negligible or irrelevant in most situations.
>
> 第 4 层负载平衡是一种流行的流量处理体系结构方法，当时商用硬件没有现在这么强大，客户端和应用程序服务器之间的交互也不那么复杂。它比更复杂的负载平衡方法(如第 7 层)需要更少的计算量，但是 CPU 和内存现在足够快和便宜，在大多数情况下，第 4 层负载平衡的性能优势已经变得微不足道或无关紧要。

In practice, we usually use **Nginx** for Layer 7 load balancing and LVS (Linux Virtual Server, a Layer 4 load balancer in the Linux kernel) for Layer 4 load balancing.

For a summary of common knowledge about Nginx, corresponding content is already available in the "Technical Interview Questions" section of [“Java Interview Guide”](https://javaguide.cn/zhuanlan/java-mian-shi-zhi-bei.html). Interested readers can take a look.

![](https://oss.javaguide.cn/github/javaguide/image-20220328105759300.png)

However, most companies do not need to use LVS; it is typically used by large companies like Alibaba, Baidu, Tencent, eBay, etc. The most commonly used solution remains Nginx.

### Client-side Load Balancing

**Client-side load balancing** is mainly applied between different services within the system and can be implemented using existing load balancing components.

In client-side load balancing, the client maintains its own list of server addresses. Before sending a request, the client selects a specific server to handle the request based on the corresponding load balancing algorithm.

The client load balancer and the services run within the same process or Java program, eliminating additional network overhead. However, the implementation of client-side load balancing may be constrained by the programming language; for instance, Spring Cloud Load Balancer can only be used with Java.

Mainstream microservice frameworks in the Java field, such as Dubbo and Spring Cloud, come with out-of-the-box client-side load balancing implementations. Dubbo has load balancing functionality built in by default, while Spring Cloud implements load balancing through components; it is optional, with Spring Cloud Load Balancer (official, recommended) and Ribbon (Netflix, deprecated) being the more commonly used options.

The following diagram illustrates a simple client-side load balancing setup based on Spring Cloud Load Balancer (Ribbon is similar):

![](https://oss.javaguide.cn/github/javaguide/high-performance/load-balancing/spring-cloud-lb-gateway.png)

## What Are the Common Algorithms for Load Balancing?

### Random Method

**Random method** is the simplest and most brute-force load balancing algorithm.

If weights are not configured, all servers have the same probability of being accessed. If weights are configured, servers with higher weights are more likely to be accessed.

The unweighted random algorithm is suitable for clusters with similar server performance, where each server bears the same load. The weighted random algorithm is suitable for clusters with unequal server performance, where the presence of weights allows for a more rational request distribution.

However, the random algorithm has a notable flaw: some machines may not be randomly selected for a period of time due to the nature of probability algorithms. Even if everyone has the same weight, such situations can still occur.

Thus, the **Round Robin method** comes into play!

### Round Robin Method

The Round Robin method involves polling each server for processing, and weights can also be set.

If weights are not configured, each request is distributed sequentially to different servers based on time. If weights are configured, the server with the higher weight will be accessed more frequently.

The unweighted round robin algorithm is suitable for clusters with similar server performance, where each server bears the same load. The weighted round robin algorithm is suitable for clusters with unequal server performance, where the presence of weights allows for a more rational request distribution.

On the basis of weighted round robin, further improved load balancing algorithms may arise, such as the smooth weighted round robin algorithm.

The smooth weighted round robin algorithm was first implemented in Nginx; you can refer to this commit: <https://github.com/phusion/nginx/commit/27e94984486058d73157038f7950a0a36ecc6e35>. If you have studied Dubbo's load balancing strategies seriously, you will find that Dubbo's weighted round robin borrowed from this algorithm and further optimized it.

![Dubbo Weighted Round Robin Load Balancing Algorithm](https://oss.javaguide.cn/github/javaguide/high-performance/load-balancing/dubbo-round-robin-load-balance.png)

### Two-time Random Method

The two-time random method adds an additional round of randomness on the basis of the random method by selecting an extra server. Then, based on the load conditions of these two servers, one is chosen as the most suitable for processing.

The benefit of the two-time random method is that it can dynamically adjust the load of backend nodes to achieve better balance. Relying solely on the single random method may lead to certain servers being overloaded while others remain idle.

### Hash Method

The request parameters are transformed into a hash value using a hash function, and the request is then directed to the server based on this hash value.

When the number of servers remains the same, requests with the same parameters will always be sent to the same server, such as requests from the same IP address or requests from the same user.

### Consistent Hash Method

Similar to the hash method, the consistent hash method also allows requests with the same parameters to be consistently sent to the same server. However, it addresses some issues present in the hash method.

Conventional hash methods reassign hash values to different servers when the number of servers changes, which clearly defeats the purpose of using the hash method. The core idea of consistent hashing is to map both data and nodes onto a hash ring and determine the respective nodes based on the order of hash values. When servers are added or removed, only the hash of the related servers is affected, without causing a complete redistribution of the hash keys across the service cluster.

### Least Connections Method

When a new request arrives, the load balancer traverses the server node list and selects the one with the least number of connections to handle the current request. In the case of equal connections, weighted random methods may be employed.

The least connected method is based on the ideal assumption that a higher number of connections indicates a higher load on the server. In practice, however, the number of connections does not accurately represent the server's actual load, as some connections may consume more system resources than others.

### Least Active Method

The least active method is similar to the least connections method but is more scientific. The least active method uses the number of active connections as the standard; active connections could be understood as the current number of requests being processed. The lower the active connection count, the stronger the processing capacity, allowing more requests to be handled by stronger servers. In the case of the same number of active connections, weighted random methods can be employed.

### Fastest Response Time Method

Differing from the least connections and least active methods, the fastest response time method selects the appropriate server based on the response time. The client maintains the response times for each server and chooses the one with the shortest response time for each request. In the case of equal response times, weighted random methods may be employed.

This algorithm ensures that requests are processed more quickly, but it may lead to an excessive concentration of traffic on high-performance servers.

## How Can Layer 7 Load Balancing Be Achieved?

Let’s briefly introduce two commonly used Layer 7 load balancing solutions: DNS resolution and reverse proxy.

Apart from the two solutions introduced, other methods such as HTTP redirection can also achieve load balancing; however, DNS resolution and reverse proxy are generally used more and are recommended.

### DNS Resolution

DNS resolution is an early implementation of Layer 7 load balancing; it is very simple.

The principle of DNS resolution for achieving load balancing is as follows: Multiple IP addresses corresponding to different servers are configured in the DNS server for the same host record. When a user requests a domain name, the DNS server uses a round-robin algorithm to return an IP address, thereby achieving round-robin load balancing.

![](https://oss.javaguide.cn/github/javaguide/high-performance/load-balancing/6997605302452f07e8b28d257d349bf0.png)

Most modern DNS resolutions support the configuration of weight for IP addresses, making request distribution in clusters with unequal server performance more rational. For instance, Alibaba Cloud DNS, which I am currently using, supports weight configuration.

![](https://oss.javaguide.cn/github/javaguide/aliyun-dns-weight-setting.png)

### Reverse Proxy

Clients send requests to the reverse proxy server, which then selects the target server. After obtaining the data, it is returned to the client. The external address exposed is the reverse proxy server's address, concealing the real server IP addresses. The reverse proxy "proxies" the target server, and this process is transparent to the client.

Nginx is the most commonly used reverse proxy server, capable of evenly distributing incoming client requests across all servers in the server cluster based on certain rules (load balancing strategies).

Reverse proxy load balancing also falls under Layer 7 load balancing.

![](https://oss.javaguide.cn/github/javaguide/nginx-load-balance.png)

## How Is Client-side Load Balancing Usually Implemented?

As mentioned earlier, client-side load balancing can be implemented using existing load balancing components.

**Netflix Ribbon** and **Spring Cloud Load Balancer** are currently the two most popular load balancing components in the Java ecosystem.

Ribbon is an established load balancing component developed by Netflix, offering comprehensive features and supporting various load balancing strategies. Spring Cloud Load Balancer is launched by Spring as a replacement for Ribbon, featuring a relatively simpler function set and fewer supported load balancing strategies.

The 7 load balancing strategies supported by Ribbon include:

- `RandomRule`: Random strategy.
- `RoundRobinRule` (default): Round-robin strategy
- `WeightedResponseTimeRule`: Weighted strategy (determining weights based on response time)
- `BestAvailableRule`: Least connections strategy
- `RetryRule`: Retry strategy (fetching services based on round-robin strategy; if the obtained service instance is null or already invalid, it keeps retrying within the specified time; if it still cannot fetch a service instance beyond the specified time, it returns null)
- `AvailabilityFilteringRule`: Availability sensitivity strategy (first filtering out unhealthy service instances, then choosing the one with a lesser number of connections)
- `ZoneAvoidanceRule`: Zone sensitivity strategy (selecting service instances based on performance and availability of the service's operating zone)

The 2 load balancing strategies supported by Spring Cloud Load Balancer include:

- `RandomLoadBalancer`: Random strategy
- `RoundRobinLoadBalancer` (default): Round-robin strategy

```java
public class CustomLoadBalancerConfiguration {

    @Bean
    ReactorLoadBalancer<ServiceInstance> randomLoadBalancer(Environment environment,
            LoadBalancerClientFactory loadBalancerClientFactory) {
        String name = environment.getProperty(LoadBalancerClientFactory.PROPERTY_NAME);
        return new RandomLoadBalancer(loadBalancerClientFactory
                .getLazyProvider(name, ServiceInstanceListSupplier.class),
                name);
    }
}
```

However, the load balancing strategies supported by Spring Cloud Load Balancer extend beyond just these two; the implementation class of `ServiceInstanceListSupplier` can also be configured to support loading balancing strategies similar to those of Ribbon. This should be gradually improved and introduced in future updates. Without looking at the official documentation, one might not realize this, which underscores the importance of reading official documentation!

Here are two official examples:

- `ZonePreferenceServiceInstanceListSupplier`: Implements region-based load balancing
- `HintBasedServiceInstanceListSupplier`: Implements hint-based load balancing

```java
public class CustomLoadBalancerConfiguration {
    // Using region-based loading balancing method
    @Bean
    public ServiceInstanceListSupplier discoveryClientServiceInstanceListSupplier(
            ConfigurableApplicationContext context) {
        return ServiceInstanceListSupplier.builder()
                    .withDiscoveryClient()
                    .withZonePreference()
                    .withCaching()
                    .build(context);
    }
}
```

For a more detailed and updated introduction to Spring Cloud Load Balancer, I recommend checking the official documentation: <https://docs.spring.io/spring-cloud-commons/docs/current/reference/html/#spring-cloud-loadbalancer>, everything is based on the official documentation.

The round-robin strategy can meet the needs of the vast majority of projects. In our actual projects, if there are no special requirements, we usually just use the default round-robin strategy. Moreover, both Ribbon and Spring Cloud Load Balancer support custom load balancing strategies.

Personally, I suggest opting for Spring Cloud Load Balancer first unless you specifically need a unique feature or load balancing strategy from Ribbon.

Lastly, let's discuss why I do not recommend using Ribbon much.

Spring Cloud 2020.0.0 version removed all Netflix components except for Eureka. Spring Cloud Hoxton.M2 was the first version to support Spring Cloud Load Balancer in place of Netflix Ribbon.

When we were learning about microservices, we certainly came across well-known components necessary for building microservices systems, such as Feign, Ribbon, Zuul, Hystrix, and Eureka from Netflix. Even now, many companies are still using these components. It is not an exaggeration to say that Netflix has led the development of microservices in the Java technology stack.

![](https://oss.javaguide.cn/github/javaguide/SpringCloudNetflix.png)

**So why is Spring Cloud so eager to remove Netflix components?** The main reason is that in 2018, Netflix announced that its core open-source components such as Hystrix, Ribbon, Zuul, and Eureka would enter maintenance mode, ceasing new feature developments and focusing solely on bug fixes. Therefore, Spring had to consider removing Netflix components.

**Spring Cloud Alibaba** is a good choice, especially for domestic companies and individual developers.

## References

- Practical Guide | eBay's Layer 4 Software Load Balancing Implementation: <https://mp.weixin.qq.com/s/bZMxLTECOK3mjdgiLbHj-g>
- HTTP Load Balancing (Nginx Official Documentation): <https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/>
- In-depth Understanding of Load Balancing - Vivo Internet Technology: <https://www.cnblogs.com/vivotech/p/14859041.html>

<!-- @include: @article-footer.snippet.md -->
