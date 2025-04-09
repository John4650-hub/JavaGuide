I don't...
type: Class loading process details
Category:
Tag:

- JVM.
  I don't...

# The life cycle of the category

From being loaded into virtual memory to unmounting memory, the entire life cycle of the class can be briefly summarized as seven stages: loading, validation, preparation, resolution, initialization, use (Using), and unloading. Of these, the three stages of validation, preparation, and resolution could be collectively referred to as Linking.

The order of the seven phases is shown in the figure below:

![Class Lifecycle](https://oss.javaguide.cn/github/javaguide/java/jvm/lifecycle-of-a-class.png)

# Class loading process

**Class files need to be loaded into a virtual machine before they can be used. How does the virtual machine load these Class files?**

The system loads Class type files in three main steps: **Load -> Connection -> Initialization**. The connection process can be divided into three steps: **Validation -> Preparation -> Parsing**.

![Class Loading Process](https://oss.javaguide.cn/github/javaguide/java/jvm/class-loading-processure.png)

For more details, see [Java Virtual Machine Specification - 5.3 Creation and Loading](https://docs.oracle.com/javasse/specs/jvms/se8/html/jvms-5.html#jvms-5.3 "Java Virtual Machine Specification - 5.3 Creation and Loading").

# Load

The first step in the load-up process is to accomplish the following three main things:

1. Acquiring binary bytes by a full name.
1. Conversion of the static storage structure represented by the byte stream to the runtime data structure of the method area.
1. Generates in memory a `Class` object representing this category as an access point for these data in the method area.

Virtual machine regulation of these three points is not specific and is therefore very flexible. For example: "Acquiring a full name to define binary bytes of this kind" does not specify where to obtain it (`ZIP`, `JAR`, `EAR`, `WAR`, network, dynamic agent technology dynamic generation, other document generation such as `JSP`, etc.), or how to obtain it.

Loading in this step is done mainly through the **class loader** that we're going to talk about later. There are many types of class loaders, and when we want to load a class, it's the class loader that is determined by **the parent delegation model** (but we can also break the parent delegation model).

> Class Loader, Parent Delegation Model is also a very important knowledge point, which is detailed in [Class Loader](https://javaguide.cn/java/jvm/classloader.html "Class Loader details"). When you read this article, you will understand one thing you can do.

Each Java class has a reference pointing to its loading `ClassLoader`. However, the array class was not created through `ClassLoader`, but was automatically created by the JVM when required, and the array class was consistent with `ClassLoader` when it was acquired through the `getClassLoader()` method.

The loading phase of a non-numeric class (actions of binary bytes during the loading phase for the acquisition of the class) is the most manageable stage, and this step allows us to complete the acquisition method (rewriting `loadClass()` method for a class loader) that can also be used to control the bytes.

Some of the actions of the loading phase and the connection phase (e.g., some byte file format validation actions) are cross-cutting, and the loading phase has not yet been completed while the connection phase may have already begun.

# Verify

**Validation is the first step in the connection phase, which aims to ensure that the information contained in the byte stream of the Class file meets all the binding requirements of the Java Virtual Machine Code and that when it is operated as code, it does not endanger the security of the Virtual Machine itself.**

This step in the validation phase is still relatively resource-intensive throughout the loading process, but it is necessary to effectively prevent malicious code enforcement. At all times, the process is first.

However, the validation phase is not a mandatory one. If all the codes (including those prepared by themselves, in third-party packages, externally loaded, dynamically generated, etc.) run by the program have been repeatedly used and validated, the use of the `-Xverify:none` parameter at the implementation stage of the production environment could be considered to close most of the types of authentication measures in order to reduce the load time of virtual machines. It should be noted, however, that `-Xverify:none` and `-noverify` are marked as deprecated in JDK 13 and may be removed in a future version of JDK.

The validation phase consists of four main stages:

1. Document format validation (Class file format check)
1. Metadata validation (bytes semantic examination)
1. Byte Code Validation (Procedure Semantic Examination)
1. Symbol reference validation (class correctness check)

!\[Class Loading Process Protection\](https://
