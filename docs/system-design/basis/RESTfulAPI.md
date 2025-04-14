---
title: A Brief Tutorial on RESTful API
category: Code Quality
---

![](https://oss.javaguide.cn/system-design/basis/2021050713553862.png)

This article briefly discusses the essential knowledge related to RESTful APIs that backend developers must possess.

Before formally introducing RESTful APIs, we first need to clarify: **What exactly is an API?**

## What is an API?

![](https://oss.javaguide.cn/system-design/basis/20210507153833945.png)

**API (Application Programming Interface)** refers to a set of rules and protocols for building and interacting with software applications.

During backend development, our primary job is to provide APIs for the frontend or other backend services, such as an API for querying user data.

![](https://oss.javaguide.cn/system-design/basis/20210507130629538.png)

However, APIs do not only represent the interfaces exposed by backend systems; methods provided in frameworks also fall under the category of APIs.

To help everyone understand, let me provide a few more examples 🌰:

1. When you search for a product on an e-commerce website, the frontend of the site calls an API provided by the backend that relates to searching for products.
1. When you develop a Java program using the JDK and want to read user input, you'll need to use the I/O-related APIs provided by the JDK.
1. ……

You can think of APIs as the bridge for communication between programs; at their core, they are just functions. Additionally, API usage is not without rules; the API provider sets the rules (such as the format of data input and output).

## What is RESTful API?

**RESTful API** is often referred to as **REST API**, which is an API built upon REST principles. What REST actually is will be discussed later, as it involves multiple concepts.

If you read articles about RESTful APIs, they may often seem obscure and hard to understand, primarily because some of the concepts related to REST are complex. Nevertheless, the knowledge about RESTful APIs we usually utilize in development is quite simple and easy to summarize!

For example, if I give you the following two APIs, you would immediately know what they are for! This showcases the strength of RESTful APIs!

```plain
GET    /classes: Lists all classes
POST   /classes: Creates a new class
```

**RESTful APIs allow you to understand what a URL does just by looking at the URL + HTTP Method and to know the result of a request by looking at the HTTP status code.**

When we design APIs during development, we should at least meet the fundamental requirements of RESTful APIs (for instance, use nouns in the interface as much as possible, use `POST` requests to create resources, use `DELETE` requests to remove resources, etc. Example: `GET /notes/id`: Retrieve information of a note with a specific ID).

## Understanding REST

**REST** stands for `REpresentational State Transfer`. This phrase translates to "Representational State Transfer."

This understanding might seem obscure; in fact, REST's full name is **Resource Representational State Transfer**, which can be simply translated as **"the resource" undergoes a "state transfer" in some "representational form" over the network**. If that’s still unclear, please continue reading; I believe the explanations below will help you understand what REST really is.

Let’s break down the concepts mentioned above to deepen our understanding. In reality, you don't need to fully grasp these concepts to understand the next content, but I recommend you take the time to understand them better if you want to discuss "RESTful API" with others!

- **Resource**: We refer to the actual object data as a resource. A resource can be either a collection or an individual entity. For example, our classes resource represents a collection, while a specific class represents an individual resource. Each resource has a specific URI (Uniform Resource Identifier) associated with it; to access this resource, you can visit its URI. For example, to get a specific class: `/class/12`. Additionally, a resource may include sub-resources, such as `/classes/classId/teachers`: Lists all teachers for a specific class.
- **Representational**: "Resources" are information entities that can manifest in various external forms. The format in which we present "resources," such as `json`, `xml`, `image`, `txt`, etc., is referred to as its "representation."
- **State Transfer**: Upon first glance, this term might confuse you. In simple terms, the state transfer in REST describes the status of server-side resources, for instance, how you change resource status through create, read, update, and delete (CRUD) operations (achieved via HTTP verbs). Note: The HTTP protocol is stateless; all resource states are stored on the server.

To summarize what the RESTful architecture entails:

1. Each URI represents a resource.
1. A certain representation of this resource, such as `json`, `xml`, `image`, `txt`, is exchanged between the client and server.
1. The client operates on server-side resources using specific HTTP verbs to achieve "representational state transfer."

## RESTful API Specifications

![](https://oss.javaguide.cn/system-design/basis/20210507154007779.png)

### Actions

- `GET`: Requests to retrieve specific resources from the server. For example: `GET /classes` (Retrieve all classes)
- `POST`: Creates a new resource on the server. For example: `POST /classes` (Create a class)
- `PUT`: Updates an existing resource on the server (the client provides the entire updated resource). For example: `PUT /classes/12` (Update the class with ID 12)
- `DELETE`: Removes a specific resource from the server. For example: `DELETE /classes/12` (Delete the class with ID 12)
- `PATCH`: Updates an existing resource on the server (the client provides the changed attributes, considered a partial update); less commonly used, so no examples here.

### Path (API Naming)

Paths, also known as "endpoints," represent specific URLs for the API. Commonly seen specifications in actual development are as follows:

1. **URLs should not contain verbs, only nouns; nouns in the API should be in plural form.** This is because resources in REST typically correspond to tables in a database, where tables represent collections of the same type of records. If an API call does not concern resources (e.g., calculations, translations, etc.), verbs can be used. For instance: `GET /calculate?param1=11&param2=33`.
1. **Avoid using capital letters; it's recommended to use hyphens (-) instead of underscores (\_).** For example, the invitation code should be `invitation-code` rather than ~~invitation_code~~.
1. **Utilize API versioning.** When our API undergoes significant changes that are incompatible with previous versions, we can implement versioning via the URL, like `http://api.example.com/v1`, `http://apiv1.example.com`. Versions don’t have to be numeric; dates or seasons can also serve as version identifiers, as long as the project team comes to a consensus.
1. **Interfaces should primarily use nouns, avoiding verbs.** RESTful API operations (HTTP Methods) concern resources (nouns) rather than actions (verbs).

Talk is cheap! Let's give a practical example to illustrate this! Suppose we have an API that provides information about classes, including details about students and teachers in those classes. Its path should be designed as follows:

```plain
GET    /classes: Lists all classes
POST   /classes: Creates a new class
GET    /classes/{classId}: Retrieves information about a specific class
PUT    /classes/{classId}: Updates information about a specific class (generally aims for complete updates)
PATCH  /classes/{classId}: Updates information about a specific class (generally aims for partial updates)
DELETE /classes/{classId}: Deletes a specific class
GET    /classes/{classId}/teachers: Lists all teachers in a specific class
GET    /classes/{classId}/students: Lists all students in a specific class
DELETE /classes/{classId}/teachers/{ID}: Deletes a specific teacher from a specific class
```

Negative example:

```plain
/getAllclasses
/createNewclass
/deleteAllActiveclasses
```

Clarifying the hierarchy of resources, for instance, if the business scope addresses schools, then a school would be a first-level resource: `/schools`, teachers: `/schools/teachers`, students: `/schools/students` would then be second-level resources.

### Filtering Information

If we need to add specific conditions when querying, it is advisable to use URL parameters. For example, to query classes with a state of active and a name of guidegege:

```plain
GET    /classes?state=active&name=guidegege
```

To implement pagination, for example:

```plain
GET    /classes?page=1&size=10 // Specify the 1st page, with 10 items per page
```

### Status Codes

**Status Code Ranges:**

| 2xx: Success | 3xx: Redirection      | 4xx: Client Error      | 5xx: Server Error         |
| ------------ | --------------------- | ---------------------- | ------------------------- |
| 200 Success  | 301 Moved Permanently | 400 Bad Request        | 500 Internal Server Error |
| 201 Created  | 304 Not Modified      | 401 Unauthorized       | 502 Bad Gateway           |
|              |                       | 403 Forbidden          | 504 Gateway Timeout       |
|              |                       | 404 Not Found          |                           |
|              |                       | 405 Method Not Allowed |                           |

## The Ultimate RESTful: HATEOAS

> **The ultimate form of RESTful is HATEOAS, but this is rarely used in actual projects.**

The above is the most fundamental aspect of RESTful APIs, and it’s also what we can easily practice in our development process. In fact, ideally, RESTful APIs should implement Hypermedia, meaning they should return links within the results that point to other API methods, allowing users to know the next steps without referring to documentation.

For example, when a user sends a request to the root directory of `api.example.com`, they would receive a response like this:

```javascript
{"link": {
  "rel":   "collection https://www.example.com/classes",
  "href":  "https://api.example.com/classes",
  "title": "List of classes",
  "type":  "application/vnd.yourformat+json"
}}
```

The above code indicates that there is a `link` attribute in the document; users can refer to this attribute to know which API to call next. `rel` indicates the relationship between this API and the current URL (collection relationship, with the collection's URL provided), `href` is the path of the API, `title` gives the API's title, and `type` indicates the returned type. The design of `Hypermedia API` is referred to as [HATEOAS](http://en.wikipedia.org/wiki/HATEOAS).

In Spring, there’s an API library called HATEOAS, which makes it easier for us to create APIs that conform to HATEOAS design. Related articles:

- [Using HATEOAS in Spring Boot](https://blog.aisensiy.me/2017/06/04/spring-boot-and-hateoas/)
- [Building REST services with Spring](https://spring.io/guides/tutorials/rest/) (Spring Official Site)
- [An Intro to Spring HATEOAS](https://www.baeldung.com/spring-hateoas-tutorial)
- [spring-hateoas-examples](https://github.com/spring-projects/spring-hateoas-examples/tree/master/hypermedia)
- [Spring HATEOAS](https://spring.io/projects/spring-hateoas#learn) (Spring Official Site)

## References

- <https://RESTfulapi.net/>

- <https://www.ruanyifeng.com/blog/2014/05/restful_api.html>

- <https://juejin.im/entry/59e460c951882542f578f2f0>

- <https://phauer.com/2016/testing-RESTful-services-java-best-practices/>

- <https://www.seobility.net/en/wiki/REST_API>

- <https://dev.to/duomly/rest-api-vs-graphql-comparison-3j6g>

<!-- @include: @article-footer.snippet.md -->
