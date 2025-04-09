[JDK 24](https://openjdk.org/projects/jdk/24/) is the third non-permanently supported version since JDK 21, along with [JDK 22](https://javaguide.cn/java/new-features/java22-23.html) and [JDK 23](https://javaguide.cn/java/new-features/java22-23.html). The next long-term support version, **JDK 25**, is expected to be released this September.

JDK 24 brought a lot of new features, 24 in total. JDK 22 and JDK 23 had only 12 features combined, and the new JDK 24 is the sum of these two. Therefore, it is very important that this version be known.

JDK 24 Overview of new features:

![JDK 24 Features](https://oss.javaguide.cn/github/java/new-features/jdk24-features.png)

The following figure shows the number of new features and the time of updating each version of JDK 8 to JDK 24:

![JDK Versions](https://oss.javaguide.cn/github/javaguide/java/new-features/jdk8~jdk24.png)

# JEP 478: Key Derivative Function API (Preview)

Key Derivative API is an encryption algorithm used to generate additional keys from the initial key and other data. Its central function is to generate multiple different keys for different encryption purposes (e.g., encryption, authentication, etc.) and to avoid the security risks associated with repeated use of the key. It's an important milestone in modern encryption, laying the foundation for the next new quantum computing environment.

The API allows developers to use the latest key-derived algorithms (e.g., HKDF and future Argon2):

```Java
// Create a KDF object using HKDF-SHA256 algorithm
KDF hkdf = KDF.getInstance("HKDF-SHA256");

// Create Extract and Expand Parameter Regulation
HKDFParameterSpec.ofExtract()
    .addIKM(initialKeyMaterial) // Setup initial key material
    .addSalt(salt) // Set salt values
    .enExpand(info, 32); // Set extension information and target length

// Assign a 32-byte AES key
SecretKey key = hkdf.deriveKey("AES", params);

// You can use the same KDF object for other key derivatives
```

# JEP 483: Loading and Linking in Advance Class

In the traditional JVM application, dynamic loading and linking of classes are required for each start. This mechanism creates significant performance bottlenecks for start-up time-sensitive applications (e.g., microservices or serverless functions). This feature has significantly reduced the cost of duplicate work and significantly reduced the start-up time of Java applications through the caching of loaded and linked classes. Tests show that start-up times can be reduced by more than 40% for large applications (e.g., Spring-based server applications).

This optimization is non-intrusive, requiring no changes to the code of the application, library, or framework, and is implemented in a consistent manner, starting with the addition of relevant JVM parameters (e.g., `-XX:+ClassDataSharing`).

# JEP 484: Class File API

The API conducted its first preview at JDK 22 ([JEP 457](https://openjdk.org/jeps/457)), and its second preview and further refinement at JDK 23 ([JEP 466](https://openjdk.org/jeps/466)). In the end, this feature is moving smoothly in JDK 24.

The objective of the Class File API is to provide a standardized set of APIs for the analysis, generation, and conversion of Java class files, replacing the previous reliance on third-party libraries (e.g., ASM) for the processing of class files.

```Java
// Creates a ClassFile object, which is the entry for the operating file.
ClassFile cf = ClassFile.of();
// Parsing byte array to ClassModel
ClassModel classModel = cf.parse(bytes);

// Build a new class file, removing all methods starting with "debug"
Byte[] newBytes = cf.build(classModel.thisClass().asSymbol());
ClassBuilder
// Walk through all classes
for (ClassElement c : classModel) 
    // Whether it's a method and the method starts with "debug"
    if (!c.methodName().stringValue().startsWith("debug")) {
        // Add to new class files
        ClassBuilder.with(c);
    }
```

# JEP 485: Stream Collector

The stream collector `Stream::gather` is a powerful new feature that allows developers to define custom intermediate operations, thus making data conversion more complex and flexible. The `Gatherer` interface, which is at the core of this feature, defines how elements are collected from the stream, the intermediate state is maintained, and the results are generated during the process.

Unlike existing built-in operations such as `filter`, `map`, or `distinct`, \`
