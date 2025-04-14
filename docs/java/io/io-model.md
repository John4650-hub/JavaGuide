---
title: Detailed Explanation of Java IO Models
category: Java
tag:
  - Java IO
  - Java Basics
---

The IO model is indeed quite difficult to understand and requires a lot of knowledge about the underlying computer systems. It took me quite a while to write this article, and I really hope to share what I know! I hope my friends can gain something from it! To write this article, I also revisited the book "UNIX Network Programming," which was really challenging—oh my goodness! It hurts my heart~

_My personal abilities are limited. If there are any areas in the article that need to be supplemented, improved, or modified, please feel free to point them out in the comments section for our mutual progress!_

## Introduction

I/O has always been a challenging concept for many friends to grasp. In this article, I will explain my understanding of I/O, hoping it can be helpful to you.

## I/O

### What is I/O?

I/O (**I**nput/**O**utput) refers to **input/output**.

**Let's first interpret I/O from the perspective of computer architecture.**

According to the Von Neumann architecture, a computer system is divided into five main parts: the arithmetic logic unit, the control unit, memory, input devices, and output devices.

![Von Neumann Architecture](https://oss.javaguide.cn/github/javaguide/java/io/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9pcy1jbG91ZC5ibG9nLmNzZG4ubmV0,size_16,color_FFFFFF,t_70.jpeg)

Input devices (like keyboards) and output devices (like monitors) are both considered external devices. Network cards and hard drives can serve as both input and output devices.

Input devices send data to the computer, while output devices receive data from the computer.

**From the perspective of computer architecture, I/O describes the process of communication between the computer system and external devices.**

**Now let's interpret I/O from the perspective of application programs.**

Based on what we learned in university about operating systems: to ensure the stability and security of the operating system, a process's address space is divided into **user space** and **kernel space**.

Typically, the applications we run operate in user space, while only kernel space can perform system-level resource-related operations, such as file management, process communication, memory management, etc. This means that to perform I/O operations, we must rely on the capabilities of kernel space.

Moreover, programs in user space cannot directly access kernel space.

When an I/O operation is to be executed, due to the lack of permission to perform these operations, a system call must be initiated to request the operating system's assistance.

Therefore, if a user process wants to perform an I/O operation, it must indirectly access kernel space through **system calls**.

In our usual development process, we most frequently encounter **disk I/O (reading and writing files)** and **network I/O (network requests and responses)**.

**From the perspective of application programs, our applications initiate I/O calls (system calls) to the operating system's kernel, which is responsible for executing the specific I/O operations. In other words, our applications merely initiate the I/O operation calls, while the actual execution of I/O is completed by the operating system's kernel.**

After an application program initiates an I/O call, it goes through two steps:

1. The kernel waits for the I/O device to be ready with data.
1. The kernel copies the data from kernel space to user space.

### What are the common I/O models?

In UNIX systems, there are five I/O models: **synchronous blocking I/O**, **synchronous non-blocking I/O**, **I/O multiplexing**, **signal-driven I/O**, and **asynchronous I/O**.

These are the five I/O models we often refer to.

## Three Common I/O Models in Java

### BIO (Blocking I/O)

**BIO belongs to the synchronous blocking I/O model.**

In the synchronous blocking I/O model, when an application initiates a read call, it will block until the kernel copies the data to user space.

![Source: "Deep Dive into Tomcat & Jetty"](https://oss.javaguide.cn/p3-juejin/6a9e704af49b4380bb686f0c96d33b81~tplv-k3u1fbpfcp-watermark.png)

This works fine when the number of client connections is low. However, when faced with hundreds of thousands or even millions of connections, the traditional BIO model is inadequate. Therefore, we need a more efficient I/O processing model to handle higher concurrency.

### NIO (Non-blocking/New I/O)

NIO in Java was introduced in Java 1.4, corresponding to the `java.nio` package,
