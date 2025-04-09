I don't...
Title: RESTful API\
Category: Code quality\
I don't...

![Image](https://oss.javaguide.cn/system-design/basis/202105071353862.png)

This article briefly talks about the knowledge of the RESTful API required for back-end programmers.

We need to get to the bottom of this: **API.**

What's API?

![Image](https://oss.javaguide.cn/system-design/basis/20105071533945.png)

**API (Application Programming Interface)**

When we're doing back-end development, the main job is to provide APIs for front-end or other back-end services, such as APIs for querying user data.

![Image](https://oss.javaguide.cn/system-design/basis/2021050713062938.png)

However, an API is not just an interface for back-end system exposure; the methods provided in the framework are also part of the API.

To make it easier for everyone to understand, I'll give you a few more examples.

1. When you search for a particular commodity through a particular electrician's website, the front end of the electrician's website calls the back end to search for the related API.
1. You will need to use the I/O-related API provided by JDK to develop Java programs and to read user input.
1. ...

You can interpret an API as a bridge between programs, which is essentially a function. Moreover, the use of APIs is not unregulated, and its rules are developed by API providers (e.g., format of data input and output).

What's RESTful API?

**RESTful API** is often also known as **REST API**, which is based on an API built by REST. What this REST is, we'll talk about later; there are a lot of concepts.

If you look at RESTful API-related articles, it's usually difficult to understand, mainly because some of the concepts involved in REST are more challenging to grasp. But, in fact, the knowledge we normally develop is very simple and easy to summarize!

For example, if I give you two of the APIs, you can see what they're for right now! That's the strength of RESTful API!

```
GET /classes: List all classes
POST /classes: New Class
```

**RESTful API allows you to see the URL + HTTP Method to know what this URL does, and to see the HTTP status code to know what the request is.**

As we design APIs in the development process, we should also meet at least the most basic requirements of RESTful API (e.g., use of terminologies in interfaces, use of `POST` requests to create resources, `DELETE` requests to delete resources, etc. Example: `GET /notes/id`: access to information from a given note ID).

# Interpretation of REST

**REST** is the abbreviation of `Representational State Transfer`. The translation of the term is “**representational transformation**.”

It's so obscure to understand that, in fact, the full name of REST is **Resource Representational State Transfer**, which is simply translated as **“Resources” to “state transfer” in some form of “expression” in the network.** If you still can't understand, please keep looking down and believe that the following explanation will make you understand exactly what REST is.

We interpret the concepts referred to above separately in order to deepen understanding. In fact, you do not need to understand the following concepts, nor do you need to understand what I am going to present in my next section. However, in order to be better able to talk to other people about "RESTful API," I suggest you understand it!

- **Resource**: We can call real target data resources. A resource can be both a pool and an individual. For example, our class classes represent a pooled form of resources, and a specific class represents individual resources. Each resource has a specific URI (Uniform Resource Identifier) counterpart, and if we need to access this resource, access to this URI is possible, such as to a specific class: `/class/12`. In addition, resources may include sub-resources such as `/classes/classId/teachers`: list information on all teachers in a given class.
- **Representation**: "Resource" is an information entity that can have multiple external manifestations. We put "resources" in specific forms, such as `json`, `xml`, `image`, `txt`, and so on; it's called "representation."
- **State Transfer**: Will it be a bad word at first sight? BB: What the heck is this? In layman's terms, the state transfer in REST describes more of the state of the server-end resource, for example, by adding, deleting, or retrieving (through HTTP verbs). PS: Internet Communications Protocol HTTP is a stateless protocol; all of which is kept on the server side.

Combining the explanations above, let's sum up what the RESTful structure is:

1. Each URI represents a resource;
1. Some form of transmission of such resources between the client and the server,
