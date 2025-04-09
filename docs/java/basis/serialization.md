I don't...
Title: Java Sequenced Details
Category:
Tag:

- Java Foundation.
  I don't...

# What's sequenced and inverse?

If we need to sustain Java objects, for example, by saving Java objects in files or transmitting Java objects online, these scenarios need to be sequenced.

In short:

- **Sequencization**: Converting data structures or objects into forms that can be stored or transmitted, usually binary bytes, or text formats such as JSON, XML.
- **Anti-serialization**: The process of converting data generated during the serialization process to the original data structure or object.

For Java, which is an object-oriented programming language, we sequence the object (Object) or the exemplified class (Class), but in a semi-object-oriented language such as C++, the structure (struct) defines the data structure type, while the class corresponds to the object type.

The following are common application scenarios for sequencing and inverse sequencing:

- The object needs to be sequenced before being connected to a network (e.g., when a remote method calls for the RPC) and the object needs to be sequenced before it is received.
- The need to sequence objects before they are stored in a file and the need to reverse sequences to read them out of the file.
- The need to sequence objects before they are stored in a database (e.g., Redis) and the need to reverse sequences to read them out of a cache database.
- Sequencing is required before an object is stored in memory, and reverse sequencing is required after it is read out of memory.

Wikipedia is like a serialized:

> **Serialization** (serialization) in computer science data processing refers to the process of converting the data structure or object state to a preferred format (e.g., to file, to buffer, or to be sent through the network) to be followed up in the same or another computer environment to restore the original state. When the byte result is retrieved according to a sequenced format, it can be used to produce a copy in the same syntax as the original object. For many objects, such as complex ones that are heavily quoted, this sequenced reconstruction process is not easy. Sequencing objects in an object does not summarize the functions to which the former original object relates. This process is also known as object grouping. The reverse operation of extracting data structures from a series of bytes is inverse sequencing (also known as solvency group, deserialization, unmarshalling).

In summary: **Sequenced primarily to transmit objects via the network or to store them in file systems, databases, memory.**

![Image](https://oss.javaguide.cn/github/javaguide/a478c74d-2c48-40ae-9374-87aacf05188c.png)

<p style="text-align:right;font-size:13px; color:gray">https://www.corejavaguru.com/java/security/interview-questions-1</p>

**What level of the TCP/IP-4 model is the sequencing protocol?**

We know that the same agreements must be adopted and respected by both parties to the network. The TCP/IP 4-level model is the following. What level is the serialization protocol?

1. Application layer
1. Transport layer
1. Network level
1. Network interface layer

![Image](https://oss.javaguide.cn/github/javaguide/cs-basics/network/tcp-ip-4-model.png)

As shown in the figure above, in the OSI Seven Layer protocol model, it is indicated that most of what is done in the layers is to process user data from the application layer into a binary stream. In turn, it is the conversion of binary streams into user data in the application layer. Isn't this just about sequencing and back sequencing?

Since the application, presentation, and session layers in the OSI 7-layer protocol model correspond to the application layer in the TCP/IP 4-layer model, the serialization protocol is part of the TCP/IP protocol application layer.

# What are the common sequencing protocols?

JDK's own serialization method is generally not used because it is inefficient and has security problems. The more common serialization protocols are Hessian, Kryo, Protobuf, and ProtoStuff, which are binary-based.

Like JSON and XML, this is the way the text class is sequenced. Although readability is better, it is less effective and generally not optional.

# JDK's own sequence

The serialization of JDK's belts requires the `java.io.Serializable` interface.

```java
@AllArgsConstructor
@NoArgsConstructor
@Getter
@Builder
@ToString
public class RpcRequest implements Serializable {
    private static final long serialVersionUID = 190512041950251207L;
    private String interfaceName;
    private String methodName;
    private Object[] parameters;
}
```

**Serial VersionUID.**

The serialization number `serialVersionUID` is the function of version control. When inverse sequencing, check whether `serialVersionUID` is consistent with the current class's `serialVersionUID`.
