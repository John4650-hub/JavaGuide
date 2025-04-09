I don't...
Title: Gradle Core Concept Summary
Category: Development tools
Head:

- Meta.
- Name: keywords
  Contact: Gradle, Groovy, Gradle Wrapper, Gradle Packer, Gradle Plugin
- Meta.
- Name: description
  Grant: Gradle is an automated project builder that runs on JVM to help us build the project automatically.
  I don't...

> This part is mainly organized on the basis of the Gradle official document, with the corresponding deletions, retaining the more important parts, which do not involve the introduction of the actual, but mainly important concepts.

Gradle, which is an optional component, can be studied according to its own needs and is currently using Maven in the country.

# Gradle Introduction

Gradle's official document describes it as:

> Gradle is an open-source [build automation](https://en.wikipedia.org/wiki/Build_automation) tool that is flexible enough to build almost any type of software.
>
> Gradle is an open-source build automation tool that is flexible enough to build almost any type of software. Gradle makes very few assumptions about what you are going to build or how to build it. This makes Gradle particularly flexible.

In short, Gradle is an automated project builder that runs on JVM to help us build the project automatically.

For developers, the main roles of Gradle are three:

1. **Project construction**: providing standard, cross-platform automated project construction.
1. **Dependency management**: easy and quick management of the resources (jar packages) on which the project depends to avoid the problem of conflicting versions between resources.
1. **Harmonized development structure**: provides a standard, harmonized project structure.

Gradle's scripts are written in Groovy or Kotlin and are very expressive and flexible.

# Groovy Introduction

Gradle is a program running on JVM that can use Groovy to write a build script.

Groovy is a scripting language that runs on JVM, based on Java's expanded dynamic language, which is very similar to Java and uses Java's library. Groovy can be used for object-oriented programming or as a pure scripting language. In terms of language design, it incorporates excellent features of Java, Python, Ruby, and Smalltalk languages, such as dynamic type conversion, closures, and metaprogramming support.

We can study Groovy in Java, and the cost of learning is relatively low. Even if the Groovy syntax is forgotten in development, it can be coded in Java.

There are many languages based on JVM, such as Groovy, Kotlin, Java, and Scala, which eventually compile and generate Java bytecode and run on JVM.

# Gradle Advantage

Gradle is a new generation of build systems with many advantages, such as efficiency and flexibility, and is widely used in Java development. Not only did Android use it as an official builder, but a growing number of Java projects, such as Spring Boot, have slowly moved to Gradle.

- In terms of flexibility, Gradle supports the production of scripts based on Groovy language, focusing on the flexibility of the build process, which is suitable for the construction of more complex projects and can complete very complex builds.
- Gradle constructs tasks that are finer in each size. All of its task source code is open, and when we get this packaged process, we can change it dynamically by modifying it.
- Gradle supports plugin mechanisms in terms of extension, so we can reuse them as simply as reusing libraries.

# Gradle Wrapper Introduction

This is what the official Gradle Wrapper describes:

The Wrapper is a mechanism that involves a reduced transformation of Gradle, which occurs if critical.

> The recommended method for implementing the Gradle build is through Gradle Wrapper (in short, “Wrapper”). The Wrapper is a script that calls the already declared Gradle version and can download it if necessary. Thus, developers can quickly start and run the Gradle project without having to follow the manual installation process, saving the company time and money.

We can call Gradle Wrapper the Gradle Packer, which will repackage Gradle and let all Gradle build methods run with the help of the Gradle Packer.

Gradle Wrapper's workflow chart is as follows (presentation of official documents of Gradle Wrapper) (https://docs.gradle.org/current/userguide/gradle_wrapper.html):

(https://oss.javaguide.cn/github/javaguide/csdn/efa7a00006b04051e2b84cd116c6ccdfc.png)

The whole process is divided into three steps:

1. First, if the specified version is not downloaded when we have just created it, then the corresponding version of the compressed package will be downloaded from Gradle's server;
1. After the download has been completed, it will be necessary to decompress and perform batch processing;
1. Each build of a follow-up project will reuse this downloaded Gradle version.

Gradle Wrapper will give us the following:

1. Standardize projects on a given Grad
