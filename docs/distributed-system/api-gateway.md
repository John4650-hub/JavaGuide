---
title: API Gateway Basic Knowledge Summary
category: Distributed
---

## What is a Gateway?

In the context of microservices, a system is split into multiple services, but features such as security authentication, traffic control, logging, and monitoring are required by each service. Without a gateway, we would need to implement these features individually in each service, leading to a lot of duplication and lacking a global view to manage these functions uniformly.

![Gateway Schematic](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway-overview.png)

Generally, a gateway can provide us with features such as request forwarding, security authentication (identity/authorization authentication), traffic control, load balancing, circuit breaking, logging, monitoring, parameter validation, and protocol conversion.

Despite all these features, the gateway mainly does two things: **request forwarding** + **request filtering**.

Since introducing a gateway adds a step of network forwarding, performance may be slightly affected (virtually negligible, especially in the case of internal network access). Additionally, we need to ensure high availability of the gateway service to avoid single points of failure.

As shown in the figure below, the outer layer of the gateway service uses Nginx (other load balancing devices/software can also be used) for load forwarding to achieve high availability. When deploying Nginx, considerations for high availability should be made to avoid single points of risk.

![Nginx-based Server Load Balancing](https://oss.javaguide.cn/github/javaguide/high-performance/load-balancing/server-load-balancing.png)

## What Functions Can a Gateway Provide?

Most gateways can provide the following functions (some functions may require the assistance of other frameworks or middleware):

- **Request Forwarding**: Forward requests to the target microservice.
- **Load Balancing**: Implement dynamic load balancing based on the load of each microservice instance or specific load balancing strategy configurations.
- **Security Authentication**: Verify user requests and only allow trusted clients to access the API, and authorization can be done using methods like RBAC.
- **Parameter Validation**: Support parameter mapping and validation logic.
- **Logging**: Record the behavior logs of all requests for future use.
- **Monitoring and Alerts**: Monitor from operational metrics, machine metrics, JVM metrics, etc., and provide a corresponding alert mechanism.
- **Traffic Control**: Control the traffic of requests, limiting the number of requests at a certain time.
- **Circuit Breaking**: Monitor request statistics in real-time, and when the configured failure threshold is reached, automatically break the circuit and return default values.
- **Response Caching**: When user requests are for some static or infrequently updated data, multiple requests for data within a certain time period may yield the same result. In such cases, responses can be cached. This way, user requests can directly receive response data at the gateway layer without going through the business service, reducing the load on the business service.
- **Response Aggregation**: In some cases, the response content that user requests may come from multiple business services. The gateway, as the caller of the business services, can aggregate the responses from multiple services and return them to the user together.
- **Gray Release**: Dynamically divert requests to different service versions (a basic form of gray release).
- **Exception Handling**: For exception responses returned by business services, the gateway layer can transform them before returning them to the user. This can obscure some details of the exceptions returned from the business side, converting them into user-friendly error messages.
- **API Documentation**: If planning to expose APIs to developers outside the organization, it's necessary to consider using API documentation tools like Swagger or OpenAPI.
- **Protocol Conversion**: Through protocol conversion, integrate backend microservices based on REST, AMQP, Dubbo, etc., providing unified services for specific clients like Web Mobile and open platforms.
- **Certificate Management**: Deploy SSL certificates to the API gateway, managing interfaces through a unified entry point, reducing the complexity during certificate replacement.

The following diagram is sourced from [Design and Implementation of the Billion-scale API Gateway Service Shepherd - Meituan Technical Team - 2021](https://mp.weixin.qq.com/s/iITqdIiHi3XGKq6u6FRVdg).

![](https://oss.javaguide.cn/github/javaguide/distributed-system/api-gateway/up-35e102c633bbe8e0dea1e075ea3fee5dcfb.png)

## What Common Gateway Systems Are There?

### Netflix Zuul

Zuul is a gateway service developed by Netflix that provides dynamic routing, monitoring, elasticity, and security, developed on the Java technology stack and can be used with components like Eureka, Ribbon, and Hystrix.

The core architecture of Zuul is as follows:

![Zuul Core Architecture](https://oss.javaguide.cn/github/javaguide/distributed-system/api-gateway/zuul-core-architecture.webp)

Zuul mainly uses filters (similar to AOP) to filter requests, thereby implementing various necessary functions of a gateway.

![Zuul Request Lifecycle](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/zuul-request-lifecycle.webp)

We can customize filters to handle requests, and the Zuul ecosystem already has many ready-made filters for us to use. For instance, rate limiting can directly use a repository written by overseas friends called [spring-cloud-zuul-ratelimit](https://github.com/marcosbarbero/spring-cloud-zuul-ratelimit) (this is just an example; generally, it is used in conjunction with Hystrix for rate limiting):

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
</dependency>
<dependency>
    <groupId>com.marcosbarbero.cloud</groupId>
    <artifactId>spring-cloud-zuul-ratelimit</artifactId>
    <version>2.2.0.RELEASE</version>
</dependency>
```

[Zuul 1.x](https://netflixtechblog.com/announcing-zuul-edge-service-in-the-cloud-ab3af5be08ee) is based on synchronous I/O, which performs poorly. [Zuul 2.x](https://netflixtechblog.com/open-sourcing-zuul-2-82ea476cb2b3) implements asynchronous I/O based on Netty, significantly improving performance.

![Zuul2 Architecture](https://oss.javaguide.cn/github/javaguide/distributed-system/api-gateway/zuul2-core-architecture.png)

- GitHub Address: <https://github.com/Netflix/zuul>
- Official Wiki: <https://github.com/Netflix/zuul/wiki>

### Spring Cloud Gateway

Spring Cloud Gateway is a gateway in the Spring Cloud ecosystem born to replace the legacy gateway **Zuul**. More precisely, it should be Zuul 1.x. Spring Cloud Gateway started earlier than Zuul 2.x.

To enhance gateway performance, Spring Cloud Gateway is built on Spring WebFlux. Spring WebFlux uses the Reactor library to implement a reactive programming model, based on Netty for synchronous, non-blocking I/O.

![](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/springcloud-gateway-%20demo.png)

Spring Cloud Gateway provides not only a unified routing method but also offers basic gateway functionalities based on a filter chain, such as security, monitoring/metrics, and rate limiting.

There aren't many differences between Spring Cloud Gateway and Zuul 2.x, both use filters to process requests. However, Spring Cloud Gateway is currently more recommended over Zuul, as the Spring Cloud ecosystem supports it more favorably.

- GitHub Address: <https://github.com/spring-cloud/spring-cloud-gateway>
- Official Site: <https://spring.io/projects/spring-cloud-gateway>

### OpenResty

According to the official introduction:

> OpenResty is a high-performance web platform based on Nginx and Lua, which integrates numerous high-quality Lua libraries, third-party modules, and most dependencies. It is used to conveniently build dynamic web applications, web services, and dynamic gateways that can handle ultra-high concurrency with high scalability.

![Relationship between OpenResty, Nginx, and Lua](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gatewaynginx-lua-openresty.png)

OpenResty is based on Nginx, primarily due to its excellent high concurrency capability. However, because Nginx is developed in C language, the barrier to secondary development is relatively high. If you want to implement some custom logic or functionality on Nginx, it requires writing C language modules and recompiling Nginx.

To solve this problem, OpenResty integrates Lua/LuaJIT perfectly into Nginx by implementing `ngx_lua` and `stream_lua` modules, allowing us to embed Lua scripts inside Nginx. This makes it possible to extend the gateway's functionality using simple Lua language, such as implementing custom routing rules, filters, caching strategies, etc.

> Lua is a very fast dynamic scripting language, with running speeds close to those of C language. LuaJIT is a just-in-time compiler for Lua that can significantly improve the execution efficiency of Lua code. LuaJIT precompiles and caches commonly used Lua functions and tool libraries, so they can be directly reused in the next call, greatly speeding up execution.

For an introduction to OpenResty and practical gateway security, it is recommended to read this article: [OpenResty Introduction and Gateway Security Practices Every Backend Should Know](https://mp.weixin.qq.com/s/3HglZs06W95vF3tSa3KrXw).

- GitHub Address: <https://github.com/openresty/openresty>
- Official Site: <https://openresty.org/>

### Kong

Kong is a high-performance, cloud-native, scalable, and rich ecosystem gateway system based on [OpenResty](https://github.com/openresty/) (Nginx + Lua), mainly consisting of three components:

- Kong Server: A server based on Nginx, used to receive API requests.
- Apache Cassandra/PostgreSQL: Used to store operational data.
- Kong Dashboard: Officially recommended UI management tool. Alternatively, RESTful methods can be used to manage the Admin API.

![](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/kong-way.webp)

Due to the default use of Apache Cassandra/PostgreSQL for data storage, Kong's entire architecture can be somewhat bulky and may introduce high availability issues.

Kong offers a plugin mechanism to extend its functionality, executed during the API request-response lifecycle. For example, to enable the Zipkin plugin on a service:

```shell
$ curl -X POST http://kong:8001/services/{service}/plugins \
    --data "name=zipkin"  \
    --data "config.http_endpoint=http://your.zipkin.collector:9411/api/v2/spans" \
    --data "config.sample_ratio=0.001"
```

Kong itself is a Lua application, but it is also an encapsulated application built on top of OpenResty. Ultimately, it empowers Nginx with programmable capabilities using the embedded Lua approach, enabling endless possibilities through plugins for things like rate limiting, security access policies, routing, load balancing, and so on. Writing a Kong plugin involves creating a custom Lua script following the Kong plugin development standards and loading it into Kong.

![](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/kong-gateway-overview.png)

In addition to Lua, Kong can also develop plugins based on Go, JavaScript, Python, and other languages, thanks to the corresponding PDK (Plugin Development Kit).

For a detailed introduction to Kong plugins, it is recommended to read the official documentation: <https://docs.konghq.com/gateway/latest/kong-plugins/>, which is quite comprehensive.

- GitHub Address: <https://github.com/Kong/kong>
- Official Site: <https://konghq.com/kong>

### APISIX

APISIX is a high-performance, cloud-native, scalable gateway system based on OpenResty and etcd.

> etcd is an open-source, high-availability distributed key-value store system developed using Go, utilizing the Raft protocol for distributed consensus.

Compared to traditional API gateways, APISIX has dynamic routing and hot plugin loading, making it especially suitable for API management in microservice systems. Additionally, APISIX integrates easily with various DevOps tools such as SkyWalking (distributed tracing system), Zipkin (distributed tracing system), and Prometheus (monitoring system).

![APISIX Architecture](https://oss.javaguide.cn/github/javaguide/distributed-system/api-gateway/apisix-architecture.png)

As an alternative project to Nginx and Kong, APISIX is currently an Apache top-level open-source project and is the fastest domestic open-source project to graduate. Many well-known domestic enterprises (such as Kingsoft, Youzan, iQIYI, Tencent, and Beike) are using APISIX to handle core business traffic.

According to the official site: "APISIX is already production-ready and has comprehensive features, performance, and architecture surpassing Kong."

APISIX also supports customizable plugin development. Developers can create plugins using Lua, and they can also do so in the following two ways to avoid the learning cost of Lua:

- Use Plugin Runner to support more mainstream programming languages (such as Java, Python, Go, etc.). This allows backend engineers to develop APISIX plugins using familiar programming languages via local RPC communication. This approach reduces development costs and improves efficiency, although there will be some performance losses.
- Use Wasm (WebAssembly) to develop plugins. Wasm is embedded in APISIX, allowing users to compile their code into Wasm bytecode to run in APISIX.

> Wasm is a binary instruction format for a stack-based virtual machine, a low-level assembly language designed to be very close to compiled machine code and very close to native performance. Originally built for browsers, as the technology matured, more use cases for server-side applications have emerged.

![](https://oss.javaguide.cn/github/javaguide/distributed-system/api-gateway/up-a240d3b113cde647f5850f4c7cc55d4ff5c.png)

- GitHub Address: <https://github.com/apache/apisix>
- Official Site: <https://apisix.apache.org/zh/>

Related articles:

- [Why Apache APISIX is the Best API Gateway?](https://mp.weixin.qq.com/s/j8ggPGEHFu3x5ekJZyeZnA)
- [With NGINX and Kong, Why Do We Still Need Apache APISIX](https://www.apiseven.com/zh/blog/why-we-need-Apache-APISIX)
- [APISIX Technical Blog](https://www.apiseven.com/zh/blog)
- [APISIX User Cases](https://www.apiseven.com/zh/usercases) (Recommended)

### Shenyu

Shenyu is an extensible, high-performance, and reactive gateway based on WebFlux, and is an Apache top-level open-source project.

![Shenyu Architecture](https://oss.javaguide.cn/github/javaguide/distributed-system/api-gateway/shenyu-architecture.png)

Shenyu extends its functionality through plugins, which are the soul of Shenyu, and the plugins are also extensible and hot-swappable. Different plugins implement different features. Shenyu comes with built-in plugins for rate limiting, circuit breaking, forwarding, rewriting, redirection, and routing monitoring.

- GitHub Address: <https://github.com/apache/incubator-shenyu>
- Official Site: <https://shenyu.apache.org/>

## How to Choose?

Among the common gateway systems introduced above, the three most commonly used are Spring Cloud Gateway, Kong, and APISIX.

For companies where Java is the primary development language, Spring Cloud Gateway is typically a good choice, with advantages such as: simplicity, maturity and stability, compatibility with the Spring Cloud ecosystem, and a mature Spring community, etc. However, Spring Cloud Gateway also has some limitations and shortcomings, generally requiring it to be used in conjunction with other gateways like OpenResty. Moreover, its performance is still somewhat inferior compared to Kong and APISIX. If performance is a significant concern, Spring Cloud Gateway is not a good choice.

Kong and APISIX offer more robust functionality, with stronger performance and a technology architecture more aligned with cloud-native principles. Kong is the pioneer of open-source API gateways, with a rich ecosystem and a large user base. APISIX, as a later entrant, is even better; according to its official site: "APISIX is already production-ready, with functionality, performance, and architecture comprehensively superior to Kong." Below is a brief comparison between the two:

- APISIX uses etcd as a configuration center, avoiding single point issues and is cloud-native friendly; whereas Kong relies on Apache Cassandra/PostgreSQL, which has single point risks and requires additional infrastructure for high availability.
- APISIX supports hot updates and achieves milliseconds-level hot update response; while Kong does not support hot updates.
- APISIX exhibits superior performance compared to Kong.
- APISIX supports a greater number of plugins, with richer functionalities.

## References

- Kong Plugin Development Tutorial \[Easy to Understand\]: <https://cloud.tencent.com/developer/article/2104299>
- API Gateway Kong Practical Application: <https://xie.infoq.cn/article/10e4dab2de0bdb6f2c3c93da6>
- Introduction to Spring Cloud Gateway Principles and Applications: <https://blog.fintopia.tech/60e27b0e2078082a378ec5ed/>
- Why Do Microservices Need an API Gateway?: <https://apisix.apache.org/zh/blog/2023/03/08/why-do-microservices-need-an-api-gateway/>

<!-- @include: @article-footer.snippet.md -->
