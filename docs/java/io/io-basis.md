I don't...
Title: Java IO Basic Summary
Category:
Tag:
- Java IO
- Java Foundation
I don't...

@include: @small-advertition.snippet.md-->

# IO Flow Profile

IO is `Input/Output`, input and output. The process by which data is entered into a computer's memory is the input, and the process by which the data is exported to external storage (e.g., database, file, remote host) is the output. Data transfer processes are similar to currents and are therefore called IO streams. IO flows in Java are divided into input and output streams, while byte and char streams are divided according to how the data is processed.

More than 40 categories of Java IO flow are derived from the following 4 abstract subcategories.

- `InputStream` / `Reader`: All input streams, the former being byte input streams and the latter being character input streams.
- `OutputStream` / `Writer`: All output streams, the former being byte output streams and the latter being character output streams.

# Bytestream

## InputStream (Byte Input Stream)

`InputStream` is used to read data (bytes of information) from a source (usually files) to memory, and `java.io.InputStream` abstract class is the parent of all byte input streams.

`InputStream` common methods:

- `read()`: returns data in the next byte of the input stream. Returns values between 0 and 255. If no bytes are read, the code returns `-1`, which means the end of the file.
- `read(byte b[])`: Reads some bytes from the input stream stored in array `b`. If the length of the array `b` is zero, it is not read. Returns `-1` if no byte is readable. If bytes are available, the maximum number of bytes read equals `b.length` and returns the number of bytes read. This method is equivalent to `read(b, 0, b.length)`.
- `read(byte b[], int off, int len)`: Adds `off` and `len` parameters (maximum bytes to read) to the `read(byte b[])` method.
- `skip(long n)`: Ignores `n` bytes in the input stream and returns the number of bytes that are actually ignored.
- `available()`: returns the number of bytes that can be read in the input stream.
- `close()`: closes the system resources associated with the release of input streams.

Starting with Java 9, `InputStream` adds several practical methods:

- `readAllBytes()`: Reads all bytes in the input stream and returns them.
- `readNBytes(byte[] b, int off, int len)`: Blocks until `len` bytes are read.
- `transferTo(OutputStream out)`: Transmits all bytes from one input stream to one output stream.

`FileInputStream` is a more commonly used byte input stream object that directly specifies the file path, can read the byte data directly, or can read into the byte array.

Example of `FileInputStream` code:

```Java
try (InputStream fis = new FileInputStream("input.txt")) {
    System.out.println("Number of remaining bytes: " + fis.available());
    long skip = fis.skip(2);
    System.out.println("The actual number of bytes skipped: " + skip);
    int content;
    while ((content = fis.read()) != -1) {
        System.out.print((char) content);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

`input.txt` Document content:

(https://oss.javaguide.cn/github/javaguide/java/image-2022041915214614.png)

Output:

``plain
Number of remaining bytes: 11
The actual number of bytes skipped: 2
The present read from file: JavaGuide
```

However, we do not normally use `FileInputStream` directly alone, usually in conjunction with `BufferedInputStream` (byte buffer input stream, as mentioned in the text).

Like the following one, which is more common in our projects, we read all bytes of the input stream through `readAllBytes()` and give them a direct value to an object of `String`.

```Java
// New BufferedInputStream Object
BufferedInputStream bufferedInputStream = new BufferedInputStream(new FileInputStream("input.txt"));
// Read the contents of the file and copy them to the String object
String result = new String(bufferedInputStream.readAllBytes());
System.out.println(result);
```

`DataInputStream` is used to read the specified type of data and cannot be used alone; it must be combined with other streams such as `FileInputStream`.

```Java
FileInputStream fileInputStream = new FileInputStream("input.txt");
// Pass fileInputStream as a parameter
DataInputStream dataInputStream = new DataInputStream(fileInputStream);
// Read any specific type of data
dataInput