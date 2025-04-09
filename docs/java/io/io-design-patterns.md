I don't...
Title: Java IO Design Pattern Summary
Category:
Tag:

- Java IO.
- Java Foundation.
  I don't...

And this article is a brief look at what we can learn from IO about the use of design patterns.

# Decorator Pattern

**Decorator pattern** can be expanded without changing the original object.

The decorator pattern expands the functions of the original category by combining successive alternatives, making it more practical in some contexts where inheritance relationships are more complex (i.e., IO, which is more complex).

For bytes, the `FilterInputStream` (reciprocal input stream) and `FilterOutputStream` (reciprocal output stream) are at the core of the decorator pattern and are used to enhance the functionality of the `InputStream` and `OutputStream` subcategories, respectively.

Our common `BufferedInputStream` (bytes buffer input stream), `DataInputStream`, and others are subcategories of `FilterInputStream`, while `BufferedOutputStream` (bytes buffer output stream), `DataOutputStream`, and others are subcategories of `FilterOutputStream`.

For example, we can enhance the `FileInputStream` function by `BufferedInputStream` (byte buffer input stream).

`BufferedInputStream` construction functions as follows:

```Java
public BufferedInputStream(InputStream in) {
    this(in, DEFAULT_BUFFER_SIZE);
}

public BufferedInputStream(InputStream in, int size) {
    if (size <= 0) {
        throw new IllegalArgumentException("Buffer size < 0");
    }
    buf = new byte[size];
}
```

As can be seen, one of the parameters of the construction function of `BufferedInputStream` is `InputStream`.

Example of `BufferedInputStream` code:

```Java
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

At this point, you can think: **Why don't we just get a `BufferedFileInputStream`?**

```Java
BufferedFileInputStream bfis = new BufferedFileInputStream("input.txt");
```

This is not a problem if there are fewer subcategories of `InputStream`. However, the `InputStream` subcategory is too numerous and inheritance relationships too complex. It's not too much trouble if we customize a corresponding buffer input stream for each subcategory.

If you are familiar with IO streams, you will find that `ZipInputStream` and `ZipOutputStream` can also enhance the capabilities of `BufferedInputStream` and `BufferedOutputStream`, respectively.

```Java
BufferedInputStream bis = new BufferedInputStream(new FileInputStream(fileName));
ZipInputStream zis = new ZipInputStream(bis);

BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(fileName));
ZipOutputStream zipout = new ZipOutputStream(bos);
```

`ZipInputStream` and `ZipOutputStream` were inherited from `InflaterInputStream` and `DeflaterOutputStream`, respectively.

```Java
class InflaterInputStream extends FilterInputStream {
    // Implementation
}

class DeflaterOutputStream extends FilterOutputStream {
    // Implementation
}
```

This is also an important feature of the decorator pattern, which allows for the use of multiple decorators for the original setup.

In order to achieve this effect, the decorator category needs the same abstract category or interface as the original type of inheritance. These IO-related decorators and the original ones, described above, are the parent categories `InputStream` and `OutputStream`.

For the character stream, `BufferedReader` may be used to increase the functionality of the `Reader` (char input stream) subcategory, and `BufferedWriter` may be used to increase the function of the `Writer` (char output stream) subcategory.

```Java
BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(fileName), "UTF-8"));
```

There are too many examples of the decorator pattern in the IO stream. And when you get to the core of the decorator pattern, you know where to use it.

# Adapter Pattern

**The Adapter Pattern** is used mainly for the coordination of incompatible types of interfaces, which you can think of in the power adapters we use on a daily basis.

There are suitable objects or classes in the adapter pattern called **Adaptee** and objects or classes for **Adapter**. The adapters are divided into object adapters and class adapters. The class adapter is achieved by an inheritance relationship and the object adapter by a composition relationship.

Character stream and byte stream interfaces differ in IO streams, where coordination is based on the adapter pattern and, more precisely, object adapter. By adapting, we can adapt byte objects to a character stream object so that we can read or write character data directly
