I don't...
Title: Java Basic Common Interview Summary (under)
Category:
Tag:

- Java Foundation.
  Head:
- Meta.
- Name: keywords
  Content: Java Abnormal Treatment, Java Panorama, Java Reflection, Java SPI Mechanism, Java Serialization, Java Counter-Sequencing, Java IO Flut, Java Genue, Java Basic Interviews, Checked Exception, Unchecked Exception, Try-With-Resources, Reflection Applications, Sequencing Protocol, BIO, NIO, AIO, IO Model
- Meta.
- Name: description
  It is hoped that the most high-quality Java basic knowledge points and interview summaries available online will help you!
  I don't...

@include: @article-header.snippet.md--

Unusual

**Overview of the structure of the Java anomaly hierarchy**:

[Java] Altitudinal hierarchy (https://oss.javaguide.cn/github/java/java/basis/types-of-exceptions-in-java.png)

What's the difference between Exception and Error?

In Java, all anomalies have a common `Throwable` class in the `java.lang` package. The category `Throwable` has two important subcategories:

- **`Exception`**: abnormalities that the procedure itself can address and can be captured through `catch`. `Exception` may also be divided into Checked Exception (inspected abnormality, which must be dealt with) and Unchecked Exception (uninspected abnormality, which may not be addressed).
- **`Error`**: `Error` is an error that the program cannot handle, ~ We cannot catch through `catch` ~ not recommended by `catch`. For example, Java Virtual Machine error (`VirtualMachineError`), Virtual Machine memory error (`OutOfMemoryError`), class definition error (`NoClassDefFoundError`), etc. When these anomalies happen, Java Virtual Machine (JVM) usually chooses to end the thread.

What difference does it make between Checked Exception and Unchecked Exception?

**Checked Exception**, i.e. inspected abnormal, Java code is being compiled without being processed by `catch` or `throws` keywords.

Like the following IO operation code:

(https://oss.javaguide.cn/github/javaguide/java/basis/checked-excide.png)

Except for `RuntimeException` and its subcategories, the other categories of `Exception` and its subcategories are subject to detection anomalies. Common checked anomalies include IO-related anomalies, `ClassNotFoundException`, `SQLException`, ...

**Unchecked Exception**, i.e. **Unchecked Aberration**, Java Code is in the process of being compiled, and even if we do not process unchecked anomalies, we can do so normally.

`RuntimeException` and its subcategories are collectively referred to as unchecked anomalies, which are common (recommend that they are often used in day-to-day development):

- `NullPointerException`
- `IllegalArgumentException` (parameter error, e.g. method reference type error)
- `NumberFormatException` (subcategory of `IllegalArgumentException` converted to numeric format error)
- `ArrayIndexOutOfBoundsException` (array cross-border error)
- `ClassCastException` (type conversion error)
- `ArithmeticException`
- `SecurityException`
- `UnsupportedOperationException` (unsupported errors such as duplicate creation of the same user)
- ...

(https://oss.javaguide.cn/github/javaguide/java/basis/unchecked-excide.png)

# Throwable's commonly used methods?

- `String getMessage()`: returns details when the anomaly occurs
- `String toString()`: returns a brief description when the anomaly occurs
- `String getLocalizedMessage()`: returns localized information of abnormal objects. Override this method using the `Throwable` subcategory to generate localized information. If the subcategory does not cover the method, the method returns the same information as 'getMessage()'.
- `void printStackTrace()`: Print abnormal information on the console for the `Throwable` object envelope

# Try-catch-finally

- `try` block: used for catching anomalies. It can then be followed by zero or more `catch` blocks or, in the absence of `catch` blocks, by a `finally` block.
- `catch` block: used to process abnormalities captured by try.
- `finally` block: The words in the `finally` block will be executed, whether or not they are captured or disposed of. When `return` statements are encountered in `try` blocks or `catch` blocks, `finally` blocks will be executed before the method returns.

Example code:

```Java
try {
    System.out.println("Throwing new RuntimeException");
    throw new RuntimeException();
} catch (Exception e) {
    System.out.println("Catch Exception-> " + e.getMessage());
} finally {
    System.out.println("Finally");
}
```

Output:

```plain
Try to do something
Catch
Finally
```

**Note: do not use return!** The return in the try statement block is ignored when both the try statement and the finally statement are available. This is
