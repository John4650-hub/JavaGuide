I don't...
I'll take you to JVM.
Category:
Tag:

- JVM.
  I don't...

> From [speak your wishes](https://juejin.im/user/5c2400fe51d4551d451758aa96) contribution, original address: <https://juejin.im/post/5e1505d0f265d5d5d744050>.

Foreword

Questions regarding the use of words or understandings in the text are welcome. It is intended to refer to it without further study, but it will throw out all the points of knowledge as efficiently as possible.

# I, JVM basic introduction

JVM is the abbreviation of Java Virtual Machine, a fictional computer, a norm. By imitating all kinds of computer functions on an actual computer...

Well, instead of saying such a professional sentence, it turns out that JVM is really like a little computer running in a window or a Linux operating system environment. It interacts directly with the operating system and does not interact directly with the hardware, which can help us to do the work with the hardware.

![Image](https://static001.geekbang.org/infoq/da/da0380a04d9c04facd2add5f6dba06fa.png)

## 1.1 Java file how it's run

So we're writing a HelloWorld.java. Okay, well, is this HelloWorld.java just like a text file, it's all in English, and it's kind of shrunk.

So our **JVM** doesn't know a text file, so it needs **to compile** and make it a binary document **HelloWorld.class**.

### 1 Class loader

If **JVM** wants to execute this **.class** file, we need to load it in a **class loader**, which, like a porter, will move all **.class** files into JVM.

![Image](https://static001.geekbang.org/infoq/2f/2f012f94376f43a25dbe1d07e0d8.png)

Method area

**Method area** is used to store data similar to metadata, such as class information, constants, static variables, post-compilation code...

The class loader moved the .class file to this block first.

# # 3 stacks

**Stack** contains mainly stored data, such as examples of objects, arrays, etc., which are in the same area as the **thread share**. Which means they're all **insulated.**

# 4

This is our code running space. Every method we've written will run in the **inn**.

We'll hear about the local method stack or the local method interface, these two terms, but we don't really deal with these two pieces, they're at the bottom of using C to work, and they don't have much to do with Java.

### 5 program counter

The main thing is to complete a load, like a pointer, pointing to the code we need to implement in the next line. Like the inn, it's \*\*-- it's \*\*-- it's \*\*-- it's \*\*-- it's \*\*-- it's \*\*-- it's \*\*-- it's \*\*- it's \*\*- it's \*\*- it's \*\*, it's \*\*, it's \*\* \*\*, it's \*\*, it's \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\* \*\*
