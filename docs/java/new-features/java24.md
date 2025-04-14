---
title: Overview of New Features in Java 24
category: Java
tag:
  - New Features in Java
---

[JDK 24](https://openjdk.org/projects/jdk/24/) is the third non-long-term support version since JDK 21, just like [JDK 22](https://javaguide.cn/java/new-features/java22-23.html) and [JDK 23](https://javaguide.cn/java/new-features/java22-23.html). The next long-term support version is **JDK 25**, expected to be released in September this year.

JDK 24 brings a considerable number of new features, totaling 24. JDK 22 and JDK 23 each had only 12 features, making the new features in JDK 24 equivalent to the sum of the previous two versions. Therefore, it is essential to understand this version.

Overview of new features in JDK 24:

![New Features in JDK 24](https://oss.javaguide.cn/github/javaguide/java/new-features/jdk24-features.png)

The following chart shows the number of new features and update times for each version from JDK 8 to JDK 24:

![](https://oss.javaguide.cn/github/javaguide/java/new-features/jdk8~jdk24.png)

## JEP 478: Key Derivation Function API (Preview)

The Key Derivation Function (KDF) API is a cryptographic algorithm used to derive additional keys from an initial key and other data. Its core function is to generate multiple distinct keys for different encryption purposes (such as encryption, authentication, etc.), avoiding security risks associated with key reuse. This is an important milestone in modern cryptography, laying the foundation for emerging quantum computing environments.

With this API, developers can use the latest key derivation algorithms (such as HKDF and future Argon2):

```java
// Create a KDF object using the HKDF-SHA256 algorithm
KDF hkdf = KDF.getInstance("HKDF-SHA256");

// Create Extract and Expand parameter specifications
AlgorithmParameterSpec params =
    HKDFParameterSpec.ofExtract()
                     .addIKM(initialKeyMaterial) // Set initial key material
                     .addSalt(salt)             // Set salt value
                     .thenExpand(info, 32);     // Set expansion info and target length

// Derive a 32-byte AES key
SecretKey key = hkdf.deriveKey("AES", params);

// The same KDF object can be used for other key derivation operations
```

## JEP 483: Early Class Loading and Linking

In traditional JVMs, applications need to dynamically load and link classes each time they start. This mechanism creates significant performance bottlenecks for applications sensitive to startup time (such as microservices or serverless functions). This feature significantly reduces the overhead of repeated work by caching loaded and linked classes, thereby reducing the startup time of Java applications. Tests have shown that for large applications (such as Spring-based server applications), startup time can be reduced by over 40%.

This optimization is non-intrusive, requiring no changes to the code of applications, libraries, or frameworks, and the startup method remains consistent, only requiring the addition of relevant JVM parameters (such as `-XX:+ClassDataSharing`).

## JEP 484: Class File API

The Class File API was first previewed in JDK 22 ([JEP 457](https://openjdk.org/jeps/457)) and further refined in JDK 23 ([JEP 466](https://openjdk.org/jeps/466)). Ultimately, this feature was successfully finalized in JDK 24.

The goal of the Class File API is to provide a standardized API for parsing, generating, and transforming Java class files, replacing the past reliance on third-party libraries (such as ASM) for class file processing.

```java
// Create a ClassFile object, the entry point for class file operations.
ClassFile cf = ClassFile.of();
// Parse byte array into ClassModel
ClassModel classModel = cf.parse(bytes);

// Build a new class file, removing all methods that start with "debug"
byte[] newBytes = cf.build(classModel.thisClass().asSymbol(),
        classBuilder -> {
            // Iterate through all class elements
            for (ClassElement ce : classModel) {
                // Check if it is a method and the method name starts with "debug"
                if (!(ce instanceof MethodModel mm
                        && mm.methodName().stringValue().startsWith("debug"))) {
                    // Add to the new class file
                    classBuilder.with(ce);
                }
            }
        });
```

## JEP 485: Stream Gatherer

The stream collector `Stream::gather(Gatherer)` is a powerful new feature that allows developers to define custom intermediate operations for more complex and flexible data transformations. The `Gatherer` interface is the core of this
