---
title: Understanding JVM in Simple Terms
category: Java
tag:
  - JVM
---

> Submitted by [Speak Your Wish](https://juejin.im/user/5c2400afe51d45451758aa96), original article: <https://juejin.im/post/5e1505d0f265da5d5d744050>.

## Introduction

If there are any issues with the wording or understanding in this article, please feel free to point them out. This article aims to mention key points without going into depth, but will try to efficiently present the knowledge.

## 1. Basic Introduction to JVM

JVM stands for Java Virtual Machine. It is a fictional computer, a specification. By simulating various computer functions on actual computers...

In simpler terms, JVM is like a small computer running in operating systems like Windows or Linux. It interacts directly with the operating system, not directly with the hardware, while the operating system helps us interact with the hardware.

![](https://static001.geekbang.org/infoq/da/da0380a04d9c04facd2add5f6dba06fa.png)

### 1.1 How Java Files Are Executed

For example, if we write a HelloWorld.java, it is essentially just a text file that contains English text with some indentation.

However, our **JVM** does not recognize text files, so it needs to be **compiled** into a **HelloWorld.class** file that it can read as a binary file.

#### ① Class Loader

If the **JVM** wants to execute this **.class** file, we need to load it into a **class loader**, which acts like a mover, bringing all the **.class** files into the JVM.

![](https://static001.geekbang.org/infoq/2f/2f012fde94376f43a25dbe1dd07e0dd8.png)

#### ② Method Area

The **Method Area** is used to store data related to metadata, such as class information, constants, static variables, and compiled code, etc.

The class loader first places the .class file into this area.

#### ③ Heap

The **Heap** mainly stores data, such as object instances and arrays. It, along with the method area, belongs to the **thread-shared area**. This means they are both **thread-unsafe**.

#### ④ Stack

The **Stack** is our code execution space. Every method we write will run in the **Stack**.

We may have heard of the terms local method stack or local method interface, but we generally do not cover these areas, as they work at a lower level using C, which is not closely related to Java.

#### ⑤ Program Counter

This mainly completes a loading task, acting like a pointer that points to the next line of code we need to execute. Like the stack, it is **thread-exclusive**, meaning each thread has its own corresponding area, avoiding concurrency and multithreading issues.

![](https://static001.geekbang.org/infoq/c6/c602f57ea9297f50bbc265f1821d6263.png)

#### Summary

1. Java files are compiled into .class bytecode files.
1. Bytecode files are moved into the JVM by the class loader.
1. The main components of the virtual machine: the method area and heap are thread-shared areas with thread safety issues, while the stack, local method stack, and counter are exclusive areas without thread safety issues. JVM tuning mainly revolves around the heap and stack.

### 1.2 Simple Code Example

A simple student class

![](https://static001.geekbang.org/infoq/12/12f0b239db65b8a95f0ce90e9a580e4d.png)

A main method

![](https://static001.geekbang.org/infoq/0c/0c6d94ab88a9f2b923f5fea3f95bc2eb.png)

The steps to execute the main method are as follows:

1. After compiling App.java to get App.class, executing App.class will start a JVM process, which will find a binary file named App.class from the classpath and load the class information of App into the method area of the runtime data area. This process is called loading the App class.
1. The JVM finds the main entry point of App and executes the main method.
1. The first statement in this main method is `Student student = new Student("tellUrDream")`, which instructs the JVM to create a Student object. However, at this point, the method area does not have the information for the Student class, so the JVM immediately loads the Student class and places its information in the method area.
1. After loading the Student class, the JVM allocates memory for a new Student instance in the heap and calls the constructor
