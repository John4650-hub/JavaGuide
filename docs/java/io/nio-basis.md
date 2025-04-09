I don't...
Title: Java NIO Core Knowledge Summary
Category:
Tag:

- Java IO
- Java Foundation
  I don't...

Before learning about NIO, basic theoretical knowledge of the I/O model is needed. If not yet understood, reference is made to the article I wrote: [Java IO Model](https://javaguide.cn/java/io/io-model.html).

# NIO Profile

In the traditional Java I/O model (BIO), I/O operations are carried out in a blocked manner. That is, when a thread executes an I/O operation, it is blocked until the operation is complete. This blockage model may lead to performance bottlenecks when dealing with multiple simultaneous connections, as it requires the creation of a thread for each connection, which incurs a cost.

To address this problem, a new I/O model - **NIO** (New IO, also known as Non-Blocking IO) - was introduced in Java 1.4. NIO made up for the synchronous blocking of I/O by providing a non-blocking, buffer-oriented, channel-based I/O in the standard Java code, which can use a small number of threads to handle multiple connections, greatly increasing I/O efficiency and concurrency.

The following is a simple comparison of BIO, NIO, and AIO's handling of client requests (a presentation on AIO can be found in the article I wrote: [Java IO Model](https://javaguide.cn/java/io/io-model.html), which is not the focus, but is sufficient.

![Comparison of BIO, NIO, and AIO](https://oss.javaguide.cn/github/javaguide/java/nio/bio-aio-nio.png)

Note that the use of NIO does not necessarily mean high performance, and its performance advantages are mainly reflected in high concurrency and delayed network environments. NIO performance is not necessarily better than the traditional BIO when the number of connections is smaller, the degree of concurrency is lower, or the speed of network transmission is faster.

# NIO Core Components

NIO consists mainly of the following three core components:

- **Buffer**: NIO reading and writing data are operated through the buffer. The read operation fills the data in the Channel into the Buffer, while writing the data from the Buffer into the Channel.
- **Channel**: A Channel is a two-way, readable data transfer channel. NIO achieves data input and output through Channels. Channels are an abstract concept that can represent a connection between a file, a socket, or another data source.
- **Selector**: Allows a thread to process multiple Channels, an event-driven I/O multiplexing model. All Channels can be registered on the Selector, which will assign a thread to handle the event.

The relationship between the three is shown in the figure below (it is okay not to understand for the time being, as it will be explained in more detail later):

![Relationship between Buffer, Channel, and Selector](https://oss.javaguide.cn/github/javaguide/java/nio/channel-buffer-selector.png)

These three components are described in detail below.

# Buffer

In the traditional BIO, the reading and writing of data is flow-oriented, divided into byte and character streams.

In the NIO library of Java 1.4, all data are processed in the buffer, which is an important difference between the new library and the previous BIO, which is somewhat similar to the buffered stream in BIO. When reading the data, NIO reads directly into the buffer. When writing data, it writes to the buffer. NIO operates through the buffer when reading and writing data.

The subcategories of `Buffer` are shown in the figure below. Of these, the most common is `ByteBuffer`, which can be used to store and operate byte data.

![Subcategories of Buffer](https://oss.javaguide.cn/github/java/java/nio/buffer-subclasses.png)

You can interpret Buffer as an array of `IntBuffer`, `FloatBuffer`, `CharBuffer`, etc., corresponding to `int[]`, `float[]`, `char[]`, etc.

In order to gain a clearer understanding of the buffer, let us briefly look at the four member variables defined in the `Buffer` class:

```java
// Invariants: mark <= position <= limit <= capacity
private int mark = -1;
private int position = 0;
private int limit = capacity;
private int capacity;
```

The four member variables have the following specific meanings:

1. Capacity (`capacity`): The maximum amount of data that the `Buffer` can store; it is set and immutable when created.
1. Limit (`limit`): The boundary in the `Buffer` where data can be read/written. In writing mode, `limit` represents the maximum amount of data that can be written, and is generally equal to `capacity` (set by `limit(int newLimit)`); in reading mode, `limit` is the size of the data actually written in the Buffer.
1. Position (`position`): The next location of
