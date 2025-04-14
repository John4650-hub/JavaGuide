---
title: Detailed Explanation of Java Serialization
category: Java
tag:
  - Java Basics
---

## What are Serialization and Deserialization?

If we need to persist Java objects, such as saving Java objects to files or transmitting Java objects over a network, these scenarios require serialization.

In simple terms:

- **Serialization**: The process of converting a data structure or object into a format that can be stored or transmitted, typically a binary byte stream, but it can also be in text formats like JSON or XML.
- **Deserialization**: The process of converting the data generated during serialization back into its original data structure or object.

In Java, an object (Object) refers to an instance of a class (Class) in an object-oriented programming language. However, in C++, a struct defines a data structure type, while a class corresponds to an object type.

Here are some common application scenarios for serialization and deserialization:

- Objects need to be serialized before being transmitted over a network (for example, during remote procedure calls, RPC). After receiving the serialized object, it needs to be deserialized.
- Objects must be serialized before being stored in files, and deserialization is required to read objects from files.
- Serialization is needed before storing objects in databases (like Redis), and deserialization is required to read objects from cache databases.
- Objects must be serialized before being stored in memory, and deserialization is needed to read them back from memory.

Wikipedia describes serialization as follows:

> **Serialization** in data processing in computer science refers to the process of converting a data structure or object state into a format that can be stored (for example, saved to a file, buffered, or sent over a network) so that it can be restored later in the same or another computer environment. When retrieving bytes according to the serialization format, it can be used to produce a copy with the same semantics as the original object. For many objects, such as complex objects with many references, this serialization reconstruction process is not easy. Object serialization in object-oriented programming does not encompass the functions related to the original object. This process is also known as object marshalling. The reverse operation of extracting a data structure from a series of bytes is deserialization (also known as unmarshalling).

In summary: **The main purpose of serialization is to transmit objects over a network or to store objects in a file system, database, or memory.**

![](https://oss.javaguide.cn/github/javaguide/a478c74d-2c48-40ae-9374-87aacf05188c.png)

<p style="text-align:right;font-size:13px;color:gray">https://www.corejavaguru.com/java/serialization/interview-questions-1</p>

**Which layer of the TCP/IP model does the serialization protocol correspond to?**

We know that both parties in network communication must adopt and adhere to the same protocol. The TCP/IP four-layer model is structured as follows; which layer does the serialization protocol belong to?

1. Application Layer
1. Transport Layer
1. Network Layer
1. Network Interface Layer

![TCP/IP Four-Layer Model](https://oss.javaguide.cn/github/javaguide/cs-basics/network/tcp-ip-4-model.png)

As shown in the diagram above, the presentation layer in the OSI seven-layer protocol model mainly processes user data from the application layer into binary streams. Conversely, it converts binary streams back into user data for the application layer. Doesn't this correspond to serialization and deserialization?

Because the application layer, presentation layer, and session layer in the OSI seven-layer protocol model correspond to the application layer in the TCP/IP four-layer model, the serialization protocol is part of the application layer of the TCP/IP protocol.

## What are the common serialization protocols?

The serialization method provided by the JDK is generally not used because of its low efficiency and security issues. Commonly used serialization protocols include Hessian, Kryo, Protobuf, and ProtoStuff, all of which are based on binary serialization.

Text-based serialization methods like JSON and XML are more readable but have poorer performance and are generally not chosen.

### JDK Built-in Serialization Method

To use the built-in serialization of the JDK, simply implement the `java.io.Serializable` interface.

```java
@AllArgsConstructor
@NoArgsConstructor
@Getter
@Builder
@ToString
public class RpcRequest implements Serializable {
    private static final long serialVersionUID = 1905122041950251207L;
    private String requestId;
    private String interfaceName;
    private String methodName;
    private Object[] parameters;
    private Class<?>[] paramTypes;
    private RpcMessageTypeEnum rpcMessageTypeEnum;
}
```

**What is the purpose of serialVersionUID?**

The serialization identifier `serialVersionUID` serves a version control purpose. During deserialization, it checks whether the `serialVersionUID` matches the current class's `serialVersionUID`. If they do not match, an \`InvalidClass
