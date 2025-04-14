---
title: Summary of Java IO Design Patterns
category: Java
tag:
  - Java IO
  - Java Basics
---

In this article, we will briefly explore what design patterns we can learn from IO.

## Decorator Pattern

The **Decorator Pattern** allows for extending the functionality of an object without changing its original structure.

The decorator pattern uses composition to extend the functionality of the original class instead of inheritance, making it more practical in scenarios with complex inheritance relationships (such as the various class hierarchies in IO).

For byte streams, `FilterInputStream` (for input streams) and `FilterOutputStream` (for output streams) are the core of the decorator pattern, used to enhance the functionality of `InputStream` and `OutputStream` subclasses.

Common subclasses of `FilterInputStream` include `BufferedInputStream` (byte buffered input stream) and `DataInputStream`, while `BufferedOutputStream` (byte buffered output stream) and `DataOutputStream` are subclasses of `FilterOutputStream`.

For example, we can enhance the functionality of `FileInputStream` using `BufferedInputStream`.

The constructor of `BufferedInputStream` is as follows:

```java
public BufferedInputStream(InputStream in) {
    this(in, DEFAULT_BUFFER_SIZE);
}

public BufferedInputStream(InputStream in, int size) {
    super(in);
    if (size <= 0) {
        throw new IllegalArgumentException("Buffer size <= 0");
    }
    buf = new byte[size];
}
```

As we can see, one of the parameters of the `BufferedInputStream` constructor is `InputStream`.

Example code for `BufferedInputStream`:

```java
try (BufferedInputStream bis = new BufferedInputStream(new FileInputStream("input.txt"))) {
    int content;
    long skip = bis.skip(2);
    while ((content = bis.read()) != -1) {
        System.out.print((char) content);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

At this point, you might wonder: **Why don't we just create a `BufferedFileInputStream`?**

```java
BufferedFileInputStream bfis = new BufferedFileInputStream("input.txt");
```

If there were only a few subclasses of `InputStream`, this approach would be fine. However, there are too many subclasses of `InputStream`, and the inheritance relationships are quite complex. If we were to create a corresponding buffered input stream for each subclass, it would be too cumbersome.

If you are familiar with IO streams, you will notice that `ZipInputStream` and `ZipOutputStream` can further enhance the capabilities of `BufferedInputStream` and `BufferedOutputStream`, respectively.

```java
BufferedInputStream bis = new BufferedInputStream(new FileInputStream(fileName));
ZipInputStream zis = new ZipInputStream(bis);

BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(fileName));
ZipOutputStream zipOut = new ZipOutputStream(bos);
```

`ZipInputStream` and `ZipOutputStream` inherit from `InflaterInputStream` and `DeflaterOutputStream`, respectively.

```java
public
class InflaterInputStream extends FilterInputStream {
}

public
class DeflaterOutputStream extends FilterOutputStream {
}
```

This is also an important feature of the decorator pattern, which allows for the nested use of multiple decorators on the original class.

To achieve this effect, the decorator class needs to inherit from the same abstract class or implement the same interface as the original class. The IO-related decorator classes and original classes mentioned above share the common parent classes `InputStream` and `OutputStream`.

For character streams, `BufferedReader` can be used to enhance the functionality of `Reader` (character input stream) subclasses, while `BufferedWriter` can enhance the functionality of `Writer` (character output stream) subclasses.

```java
BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(fileName), "UTF-8"));
```

There are numerous examples of the application of the decorator pattern in IO streams, and there is no need to memorize them; it is completely unnecessary! Once you understand the core of the decorator pattern, you will naturally recognize where it is applied when using it.

## Adapter Pattern

The **Adapter Pattern** is primarily used to coordinate the work of classes with incompatible interfaces, similar to the power adapters we commonly use in daily life.

In the adapter pattern, the object or class being adapted is called the **Adaptee**, while the object or class that acts on the Adaptee is called the **Adapter**. There are two types of adapters: class adapters and object adapters. Class adapters use inheritance to implement, while object adapters use composition.

The interfaces of character streams and byte streams in IO are different, and their ability to work together is based on the adapter pattern, more specifically, the object adapter. Through the adapter, we can adapt a byte stream
