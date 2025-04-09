I don't...

# Title: Summary of Basic Knowledge of the API Gateway

## Category: Distribution

I don't...

## What's a Gateway?

In the context of microservices, a system is broken down into multiple services, but functions such as security certification, traffic control, logs, monitoring, etc., are required for every service. Without a gateway, we need to handle these functions individually in each service, which leads to a lot of duplication and lacks a global view to manage these functions.

![API Gateway Overview](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/api-gateway-overview.png)

As a general rule, the gateway can provide us with functions such as request forwarding, security authentication (identity/authority authentication), traffic control, load balancing, downgrading, logs, monitoring, verification of parameters, protocol conversion, etc.

These functions are described above, and in fact, the gateway has done two main things: **request transmission** + **request filtering**.

Since the introduction of the gateway will result in an additional step in the network, performance will be impacted (almost negligible, especially in the case of Intranet access). In addition, we need to ensure the high availability of gateway services and avoid single-point risks.

As shown in the figure below, the gateway service outer layer is carried out through Nginx (other load balancing devices/software are also applicable) to achieve high availability. Nginx should, to the extent possible, be deployed with a view to being highly usable and avoiding single-point risks.

![A Service-End Load Balance Based on Nginx](https://oss.javaguide.cn/github/javaguide/high-perform/load-balancing/server-load-balancing.png)

## What Does the Gateway Provide?

The vast majority of gateways provide these functions (some of which require other frameworks or intermediaries):

- **Request Transmission**: Forward requests to the target microservice.
- **Load Balancing**: A dynamic load balance of requests based on the load of each microservice or a specific load balancing strategy configuration.
- **Security Authentication**: Authentication of user requests, allowing access to the API only by a credible client, which can be authorized by, for example, using RBAC.
- **Parameter Validation**: Supporting parameter mapping and validation logic.
- **Log Records**: All requested log entries are recorded for subsequent use.
- **Surveillance Alert**: Monitoring and provision of accompanying alarm mechanisms in terms of operational indicators, machine indicators, JVM indicators, etc.
- **Flow Control**: Control of the flow of requests, i.e., limit the number of requests at a given time.
- **Downgrading**: Real-time monitoring of statistical information requested, automatically downgrading after reaching the allocated failure threshold and returning the default value.
- **Response Caching**: When users request static or less frequently updated data, it is likely that the same data will be obtained over time. In this case, the response could be cached. This would allow user requests to receive response data directly at the gateway level and would eliminate the need to access business services, reducing the burden on them.
- **Response Aggregation**: In some cases, the content of the response requested by users may come from multiple business services. As a caller for business services, the gateway integrates the responses of multiple services and returns them to users together.
- **Grayscale Release**: Dynamic diversion of requests to different service versions (the most basic grayscale release).
- **Error Handling**: The unusual response from business service returns can be converted at the gateway level before returning to the user. This allows the unusual details of some business-side returns to be hidden and converted into user-friendly error messages.
- **API Documentation**: If you plan to expose APIs to developers outside the organization, then you have to consider using API documentation such as Swagger or OpenAPI.
- **Protocol Transformation**: Integration of backend services based on different styles and technologies such as REST, AMQP, Dubbo, providing unified services to specific clients such as Web, Mobile, and Open Platform.
- **Certificate Management**: Deployment of SSL certificates to the API gateway with a unified access management interface, which reduces the complexity of certificate replacement.

The following figure is based on the article "The Design and Realization of the [Hundred-Billion-Scale] API Gateway Service, Shepherd - the American Mission Technical Team - 2021." ![API Gateway Design](https://oss.javaguide.cn/github/javaguide/distributed-system/api-gateway/up-35e102c633bbe8e0dea1e075ea3fee5dcfb.png)

## What Are the Common Gateway Systems?

### Netflix Zuul

Zuul is a project developed by Netflix that provides dynamic routing, monitoring, elasticity, and secure gateway services based on the Java technology stack, which can be used in conjunction with the Eureka, Ribbon, and Hystrix components.

The core structure of Zuul is as follows:

!\[Zuul Core Architecture\](https://oss.jav
