---
title: Summary of Core Knowledge of Java NIO
category: Java
tag:
  - Java IO
  - Java Basics
---

Before learning NIO, it is necessary to understand the basic theoretical knowledge of computer I/O models. If you are not familiar with it, you can refer to the article I wrote: [Detailed Explanation of Java IO Models](https://javaguide.cn/java/io/io-model.html).

## Introduction to NIO

In the traditional Java I/O model (BIO), I/O operations are performed in a blocking manner. This means that when a thread performs an I/O operation, it will be blocked until the operation is complete. This blocking model can lead to performance bottlenecks when handling multiple concurrent connections, as a thread needs to be created for each connection, and both thread creation and switching incur overhead.

To address this issue, a new I/O model was introduced in Java 1.4 — **NIO** (New IO, also known as Non-blocking IO). NIO compensates for the shortcomings of synchronous blocking I/O by providing non-blocking, buffer-oriented, channel-based I/O in standard Java code, allowing a small number of threads to handle multiple connections, significantly improving I/O efficiency and concurrency.

The following diagram is a simple comparison of how BIO, NIO, and AIO handle client requests (for an introduction to AIO, you can refer to my article: [Detailed Explanation of Java IO Models](https://javaguide.cn/java/io/io-model.html), which is not the focus, just for understanding).

![Comparison of BIO, NIO, and AIO](https://oss.javaguide.cn/github/javaguide/java/nio/bio-aio-nio.png)

⚠️ Note: Using NIO does not necessarily mean high performance; its performance advantages are mainly reflected in high concurrency and high-latency network environments. When the number of connections is small, concurrency is low, or network transmission speed is fast, NIO's performance may not necessarily be better than traditional BIO.

## Core Components of NIO

NIO mainly includes the following three core components:

- **Buffer**: NIO reads and writes data through buffers. During read operations, data from the Channel is filled into the Buffer, while during write operations, data from the Buffer is written into the Channel.
- **Channel**: A Channel is a bidirectional data transmission channel that can be read from and written to. NIO uses Channels to implement data input and output. A channel is an abstract concept that can represent a connection between files, sockets, or other data sources.
- **Selector**: Allows a thread to handle multiple Channels based on an event-driven I/O multiplexing model. All Channels can be registered with the Selector, which allocates threads to handle events.

The relationship among the three components is shown in the diagram below (it's okay if you don't understand it for now; it will be explained in detail later):

![Relationship among Buffer, Channel, and Selector](https://oss.javaguide.cn/github/javaguide/java/nio/channel-buffer-selector.png)

Now, let's take a closer look at these three components.

### Buffer

In traditional BIO, data reading and writing are stream-oriented, divided into byte streams and character streams.

In the NIO library of Java 1.4, all data is processed using buffers, which is an important distinction between the new library and the previous BIO, somewhat similar to buffered streams in BIO. NIO reads data directly into the buffer. When writing data, it is written into the buffer. When using NIO for reading and writing data, operations are performed through buffers.

The subclasses of `Buffer` are shown in the diagram below. Among them, the most commonly used is `ByteBuffer`, which can be used to store and manipulate byte data.

![Subclasses of Buffer](https://oss.javaguide.cn/github/javaguide/java/nio/buffer-subclasses.png)

You can think of a Buffer as an array, where `IntBuffer`, `FloatBuffer`, `CharBuffer`, etc., correspond to `int[]`, `float[]`, `char[]`, and so on.

To better understand the buffer, let's take a brief look at the four member variables defined in the `Buffer` class:

```java
public abstract class Buffer {
    // Invariants: mark <= position <= limit <= capacity
    private int mark = -1;
    private int position = 0;
    private int limit;
    private int capacity;
}
```

The specific meanings of these four member variables are as follows:

1. Capacity (`capacity`): The maximum amount of data that the `Buffer` can store, set when the `Buffer` is created and cannot be changed.
1. Limit (`limit`): The boundary for reading/writing data in the `Buffer`. In write mode, `limit` represents the maximum amount of data that can be written, generally equal to `capacity` (can be set using the \`limit(int new
