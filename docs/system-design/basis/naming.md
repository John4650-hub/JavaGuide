I don't...
title: Code Naming Guide
Category: Code quality
I don't...

I remember when I was at work, during the project Code Review, I was often "dissed" because variable names were irregular!

The reason for this is that it is due to inexperience, and that the university does not pay much attention to these issues when writing about the project, thinking that it is only necessary to do so.

But it's not the same at work, and the project team's requirements for code quality are high for the readability and maintenance of code!

A new intern from the project team was often "dissed" in Code Review because of the irregular naming of the variable, which reminds me of the day I first got to the company to write code.

So I wrote this short article on variable naming norms, hoping to help fellow partners who are also troubled.

Indeed, there are so many things in the programming process that give us headaches, like naming, maintaining the code of others, writing tests, communicating with others, etc.

It was said that the hardest thing that was previously selected on the Quora website by nearly 5,000 programmers was "naming."

Martin Fowler, the author of the famous "Refactoring," once mentioned two of the most difficult things in the CS field in an article in [Two Hard Things](https://martinfowler.com/bliki/TwoHardThings.html): first, **cache invalidation** and second, **naming things**.

(https://oss.javaguide.cn/java-guide-blog/marting-naming.png)

That sentence was actually quoted by others, and there were many similar expressions. In the area of distributed systems, for example, there are two most difficult things: first, to ensure that the message sequences are correct, and second, to ensure a strict one-time transmission.

(https://oss.javaguide.cn/java-guide-blog/202062910484445.png)

Today we're going to take out "**naming**" and talk!

This article matches my previous release. [Clypted] Five minutes, named two hours? The article is better read!

# Why do you need to focus on naming?

We need to figure out why naming in programming is important and what it means for our coding.

**Why is naming important?** Because **a good name is a note, and when you're named, you know what your variable, method, or class does!**

In short, it's **someone else can tell you what your code means by your name** (provided, however, that the person also has a basic knowledge of English and is familiar with some of the common words in programming).

A simple example of the importance of naming.

The book "Clean Code" states:

> **Good code itself is a note. We try to regularize and glorify our own code to reduce unnecessary comments.**
>
> **If the programming language is sufficiently expressive, there is no need for comments, as much as possible by code.**
>
For example:
>
> Remove the following complex comment, just create a function of the same thing as the note says.
>
> ```java
> // Check to see if the employee is eligible for full benefits
> if ((employee.flags & HOURLY_FLAG) & (employee.age > 65))
> ```
>
> Should be replaced with
>
> ```java
> if (employee.isEligibleForFullBenefits())
> ```

# Common naming rules and applicable scenes

Only three of the most common naming norms are presented here.

# CamelCase

The CamelCase method should be our most common one, which uses a case-by-case format to distinguish between words, and which does not use space-separated or character-connected naming between words.

UpperCamelCase

**Class names need to be named with UpperCamelCase.**

Example:

```java
ServiceDiscovery, ServiceInstance, LruCacheFactory
```

Example:

```java
I'm not sure I'm going to be able to do this.
```

# LowerCamelCase

**Method names, parameter names, member variables, and local variables need to be named using lowerCamelCase.**

Example:

```java
getUser Info()
createCustomThreadPool()
setNameFormat
generalService;
```

Example:

```java
getUser Info(), createCustomThreadPool(), setNameFormat(String nameFormat)
service user_service
```

Snake Naming (snake_case)

**Test method names, constants, and enumeration names require snake_case naming.**

In snake_case naming, the words are connected by underscores, such as `should_get_200_status_code_when_request_is_valid`, `CLIENT_CONNECT_SERVER_FAILURE`.

The advantage of snake_case naming is the number of words required to name. More often, for example, I'm going to give you a look at the name above, "shouldGet200StatusCodeWhenRequestIsValid."

How are you feeling? Is it less readable than using snake_case names?

Example:

```java
@Test
void should_get_200_status_code_when_request_is_valid() {
    //