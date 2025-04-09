I don't...
Title: Spring Cloud Gateway Summary of Common Issues
Category: Distribution
I don't...

> Reconstruct this article from [6000 words] | | | | 深入 深入 深入 深入 深入 深入 深入 深入 > > > > > > > > > > > > > > > > > > > > > > > > > >

# Spring Cloud Gateway?

Spring Cloud Gateway is a gateway in the Spring Cloud ecosystem, born to replace the old-licensed gateway **Zuul**. Zuul 1.x, to be precise. Spring Cloud Gateway started earlier than Zuul 2.x.

To enhance the performance of the gateway, Spring Cloud Gateway is based on Spring WebFlux. Spring WebFlux uses the Reactor library to achieve a responsive programming model with a foundation based on Netty to achieve synchronized, unobstructed I/O.

![Spring Cloud Gateway Demo](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/springcloud-gateway-%20demo.png)

Spring Cloud Gateway not only provides a uniform routing approach but also a filter-based approach that provides essential functionality for the gateway, such as security, monitoring/indicators, and rate limiting.

Spring Cloud Gateway and Zuul 2.x are not very different and also handle requests through filters. However, Spring Cloud Gateway, instead of Zuul, is now more recommended, and the Spring Cloud ecosystem is more supportive of it.

- GitHub address: <https://github.com/spring-cloud/spring-cloud-gateway>
- Official: <https://spring.io/projects/spring-cloud-gateway>

# Spring Cloud Gateway's Workflow?

The workflow for Spring Cloud Gateway is as follows:

![Spring Cloud Gateway's Workflow](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/spring-claud-gateway-workflow.png)

This is a map from the Spring official blog, originally available at <https://spring.io/blog/2022/08/26/creating-a-custom-spring-claud-gateway-filter>.

Specific process analysis:

1. **Route Judgement**: After the client's request has reached the gateway, it is processed by Gateway Handler Mapping, which will make an assertion (Predicate) as to which route rules are met, which is a service at the back end of the map.
1. **Request Filter**: The request then reaches the Gateway Web Handler, which contains a number of filters that form the Filter Chain, which can intercept and modify requests, such as adding headers, verifying parameters, etc., and is somewhat like cleaning sewage. The request is then forwarded to the actual back-end service. These filters can logically be called Pre-Filters; Pre can be understood as "before..."
1. **Service Processing**: Back-end services process requests.
1. **Response Filter**: After the end of the back-end processing, the filter returned to Gateway is reprocessed and logically known as Post-Filters; Post can be understood as “after...”.
1. **Reaction Returns**: The response is filtered and returned to the client.

Summarizing: Client requests can be mapped to specific services by identifying the right route through matching rules. The request is then processed by a filter and forwarded to a specific service, which is then processed by a filter and then returned to the client.

# Spring Cloud Gateway

The word Predicate sounds so deep that it is a programming term that we would never use in our lives. To put it simply, it is to judge an expression, whether it is true or false, and if it is true, do it, or do not do it.

In Gateway, if the request sent by the client fulfills the terms of the assertion, it is mapped to the specified route and can be forwarded to the specified service for processing.

The following is an example of an asserted configuration, with two route rules, with one predicate asserting a configuration, matching the first route `route_thirdparty` when the request URL contains `api/thirdPart`.

![Assertions Configuration Example](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/spring-claud-gateway-priricate-example.png)

The usual route-based assertion rules are as follows:

![Predicate Rules](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/spring-claud-gateway-predation-rules.png)

# Spring Cloud Gateway's Route and the Assertion?

Route's route and Predicate's assertion correspondence is as follows:

![Routes and Alleged Correspondence](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway/spring-claud-gateway-prite-route.png)

- **One Pair More**: One route rule may contain multiple assertions. As the route 1 above has been configured with three
