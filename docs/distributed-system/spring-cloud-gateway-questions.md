---
title: Summary of Common Issues with Spring Cloud Gateway
category: Distributed
---

> This article is a refined version of [6000 words | 16 images | In-depth Understanding of the Principles of Spring Cloud Gateway - Wukong Talks Architecture](https://mp.weixin.qq.com/s/XjFYsP1IUqNzWqXZdJn-Aw).

## What is Spring Cloud Gateway?

Spring Cloud Gateway is a gateway in the Spring Cloud ecosystem, designed to replace the legacy gateway **Zuul**. More precisely, it aims to replace Zuul 1.x, as Spring Cloud Gateway was developed earlier than Zuul 2.x.

To enhance gateway performance, Spring Cloud Gateway is built on Spring WebFlux. Spring WebFlux utilizes the Reactor library to implement a reactive programming model, and is based on Netty for synchronous non-blocking I/O at the core.

![](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/springcloud-gateway-%20demo.png)

Spring Cloud Gateway not only provides a unified routing method but also offers basic gateway functions based on a chain of filters, such as security, monitoring/metrics, and rate limiting.

The differences between Spring Cloud Gateway and Zuul 2.x are minimal, as both handle requests through filters. However, Spring Cloud Gateway is currently more recommended than Zuul, as the Spring Cloud ecosystem offers better support for it.

- GitHub address: <https://github.com/spring-cloud/spring-cloud-gateway>
- Official website: <https://spring.io/projects/spring-cloud-gateway>

## What is the workflow of Spring Cloud Gateway?

The workflow of Spring Cloud Gateway is illustrated in the following diagram:

![Workflow of Spring Cloud Gateway](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/spring-cloud-gateway-workflow.png)

This is an image from the official Spring blog, original text address: <https://spring.io/blog/2022/08/26/creating-a-custom-spring-cloud-gateway-filter>.

Specific process analysis:

1. **Route Determination**: Once the client's request reaches the gateway, it first passes through the Gateway Handler Mapping, where predicate assertions are evaluated to see which route rule applies. This route maps to a certain backend service.
1. **Request Filtering**: The request then reaches the Gateway Web Handler, which has many filters that form a filter chain. These filters can intercept and modify requests, such as adding request headers, validating parameters, etc., akin to purifying wastewater. The request is then forwarded to the actual backend service. These filters are logically termed Pre-Filters, where Pre can be understood as "before...".
1. **Service Processing**: The backend service processes the request.
1. **Response Filtering**: After the backend has finished processing and returned the result, it is processed again by the Gateway's filters, logically termed Post-Filters, where Post can be understood as "after...".
1. **Response Return**: After filtering, the response is returned to the client.

In summary: the client's request first finds the appropriate route by matching rules, mapping it to a specific service. After passing through filter processing, the request is forwarded to the specific service, which is processed, filtered again, and ultimately returned to the client.

## What are predicates in Spring Cloud Gateway?

The term predicate might sound complex; it is a programming term that we do not commonly use in our daily life. Simply put, it refers to performing an if-judgment on an expression, yielding a true or false result. If true, an action is taken; if false, another action is executed.

In the Gateway, if the client's request meets the predicate's conditions, it maps to the specified router, enabling forwarding to the designated service for processing.

An example of predicate configuration is shown below, which configures two routing rules, with a predicates assertion that matches when the request URL contains `api/thirdparty`, corresponding to the first route `route_thirdparty`.

![Predicate Configuration Example](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/spring-cloud-gateway-predicate-example.png)

Common routing predicate rules are shown in the following diagram:

![Routing Predicate Rules of Spring Cloud Gateway](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/spring-cloud-gateway-predicate-rules.png)

## What is the relationship between routes and predicates in Spring Cloud Gateway?

The correspondence between Route and Predicate is as follows:

![Correspondence between Routes and Predicates](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/spring-cloud-gateway-predicate-route.png)

- **One-to-Many**: One routing rule can contain multiple predicates. As shown in the image, Route1 is configured with three predicates.
- **Simultaneous Satisfaction**: If a routing rule has multiple predicates, all must be satisfied for a match. In the image, for Route2, the client's request must satisfy both predicates to match Route2.
- **First Match Success**: If a request can match multiple routes, the first successfully matching route is chosen. In the image, the client's request satisfies both Route3 and Route4's predicates, but since Route3's configuration appears earlier in the configuration file, it will only match Route3.

## How does Spring Cloud Gateway achieve dynamic routing?

When using Spring Cloud Gateway, official documentation often provides solutions based on configuration files or code configurations.

As the entry point for microservices, Spring Cloud Gateway should aim to avoid restarts. However, current configuration changes require server restarts, which cannot meet the dynamic refresh and real-time change demands in actual production. Thus, we need to dynamically configure the gateway during its runtime.

There are many ways to achieve dynamic routing, and one recommended method is based on the Nacos registry. Spring Cloud Gateway can retrieve service metadata (such as service name, path, etc.) from the registry and automatically generate routing rules based on this information. Thus, when you add, remove, or update service instances, the gateway will automatically perceive these changes and adjust the routing rules accordingly, eliminating the need for manual maintenance of routing configurations.

In fact, these complex steps do not require manual implementation since dynamic configuration changes can be achieved through Nacos Server and Spring Cloud Alibaba Nacos Config. For the official documentation, visit: <https://github.com/alibaba/spring-cloud-alibaba/wiki/Nacos-config>.

## What are the filters in Spring Cloud Gateway?

Filters can be categorized into two types based on requests and responses:

- **Pre Type**: Intercepts and modifies requests before they are forwarded to microservices, including parameter validation, permission checks, traffic monitoring, logging, and protocol conversion.
- **Post Type**: After the microservice processes the request and returns a response to the gateway, the gateway can perform further processing, such as modifying the response content or headers, logging, and traffic monitoring.

Another classification divides filters based on their scope of action:

- **GatewayFilter**: Local filters applied to a single route or a group of routes. The red-colored ones are commonly used filters.
- **GlobalFilter**: Global filters applied across all routes.

### Local Filters

Common local filters are illustrated in the following diagram:

![](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/spring-cloud-gateway-gatewayfilters.png)

How is it used? Here’s an example: if the URL matches successfully, it removes "api" from the URL.

```yaml
filters: # Filters
  - RewritePath=/api/(?<segment>.*),/$\{segment} # Replace "api" in the redirection path with empty
```

Of course, we can also define custom filters, but this article does not elaborate on that.

### Global Filters

Common global filters are illustrated in the following diagram:

![](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/spring-cloud-gateway-globalfilters.png)

The most common use of global filters is for load balancing. The configuration is as follows:

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: route_member # Third-party microservice routing rule
          uri: lb://passjava-member # Load balancing, forward requests to the registered passjava-member service in the registry
          predicates: # Predicates
            - Path=/api/member/** # If the frontend request path includes api/member, this routing rule applies
          filters: # Filters
            - RewritePath=/api/(?<segment>.*),/$\{segment} # Replace "api" in the redirection path with empty
```

Here we have a keyword `lb`, which utilizes the global filter `LoadBalancerClientFilter`. When this route matches, the request will be forwarded to the passjava-member service, supporting load-balanced forwarding, meaning it first resolves passjava-member to the actual microservice's host and port before forwarding it to the actual microservice.

## Does Spring Cloud Gateway support rate limiting?

Spring Cloud Gateway comes with a built-in rate limiting filter, corresponding to the interface `RateLimiter`. The `RateLimiter` interface has only one implementation class `RedisRateLimiter` (a rate limiter based on Redis + Lua), providing relatively simple and not very user-friendly rate limiting functionality.

Starting from Sentinel version 1.6.0, Sentinel introduced an adaptation module for Spring Cloud Gateway that provides two dimensions for rate limiting: route dimension and custom API dimension. This means Spring Cloud Gateway can combine with Sentinel to achieve more powerful gateway traffic control.

## How do we customize global exception handling in Spring Cloud Gateway?

In SpringBoot projects, we can capture global exceptions by simply configuring `@RestControllerAdvice` and `@ExceptionHandler`. However, this method is not applicable under Spring Cloud Gateway.

Spring Cloud Gateway provides various global handling methods, with a commonly used one being to implement `ErrorWebExceptionHandler` and override its `handle` method.

```java
@Order(-1)
@Component
@RequiredArgsConstructor
public class GlobalErrorWebExceptionHandler implements ErrorWebExceptionHandler {
    private final ObjectMapper objectMapper;

    @Override
    public Mono<Void> handle(ServerWebExchange exchange, Throwable ex) {
    // ...
    }
}
```

## References

- Official Spring Cloud Gateway documentation: <https://cloud.spring.io/spring-cloud-gateway/reference/html/>
- Creating a custom Spring Cloud Gateway Filter: <https://spring.io/blog/2022/08/26/creating-a-custom-spring-cloud-gateway-filter>
- Global exception handling: <https://zhuanlan.zhihu.com/p/347028665>

<!-- @include: @article-footer.snippet.md -->
