---
title: Code Naming Guidelines
category: Code Quality
---

I still remember the time when I first started working. During project code reviews, I was often "dissed" for improper variable naming!

The reason was my lack of experience at that time, and during my university days, I didn't pay much attention to these issues, thinking that as long as I implemented the functionality, it would be fine.

However, it's different in the workplace. For the sake of code readability and maintainability, the project team has high standards for code quality!

Recently, a new intern in our project team was also frequently "dissed" during code reviews for improper variable naming, which reminded me of my early days writing code at the company.

So, I wrote this article on variable naming conventions, hoping to provide some help to those who are similarly troubled.

Indeed, there are many headaches in the programming process, such as naming, maintaining others' code, writing tests, and communicating with others, etc.

It is said that a survey on Quora, conducted by nearly 5,000 programmers, found that the hardest thing is "naming."

The famous author of "Refactoring," Martin Fowler, mentioned in his article [Two Hard Things](https://martinfowler.com/bliki/TwoHardThings.html) that there are two most difficult things in the CS field: one is **cache invalidation**, and the other is **naming programs**.

![](https://oss.javaguide.cn/java-guide-blog/marting-naming.png)

This statement is actually a quote from someone else, and there are many similar expressions. For example, in the field of distributed systems, the two hardest things are: one is **guaranteeing message order**, and the other is **exactly-once delivery**.

![](https://oss.javaguide.cn/java-guide-blog/20210629104844645.png)

Today, let's focus on "**naming**"!

This article is best read in conjunction with my previous article [“Coding for 5 Minutes, Naming for 2 Hours? The Most Comprehensive Java Naming Convention Reference Ever!”](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247486449&idx=1&sn=c3b502529ff991c7180281bcc22877af&chksm=cea2443af9d5cd2c1c87049ed15ccf6f88275419c7dbe542406166a703b27d0f3ecf2af901f8&token=999884676&lang=zh_CN#rd) for better understanding!

## Why is Naming Important?

We need to understand why naming in programming is important and what significance it holds for our coding work.

**Why is naming important?** Because **good naming serves as documentation; when others see your naming, they immediately understand what your variables, methods, or classes do!**

In simple terms, **others can understand what your code expresses based on your naming** (provided that the person has basic English knowledge and is familiar with common programming terms).

Let me give a simple example to illustrate the importance of naming.

The book "Clean Code" clearly states:

> **Good code itself is documentation; we should strive to standardize and beautify our code to reduce unnecessary comments.**
>
> **If the programming language is expressive enough, comments are unnecessary; we should try to explain through code.**
>
> For example:
>
> Instead of the complex comment below, just create a function that describes the same thing as the comment:
>
> ```java
> // check to see if the employee is eligible for full benefits
> if ((employee.flags & HOURLY_FLAG) && (employee.age > 65))
> ```
>
> It should be replaced with:
>
> ```java
> if (employee.isEligibleForFullBenefits())
> ```

## Common Naming Conventions and Applicable Scenarios

Here, I will introduce the three most common naming conventions.

### CamelCase

CamelCase is probably the most common naming convention. This naming style uses a mix of uppercase and lowercase letters to distinguish between words, without using spaces or connecting characters.

#### UpperCamelCase

**Class names should use UpperCamelCase.**

Correct examples:

```java
ServiceDiscovery, ServiceInstance, LruCacheFactory
```

Incorrect examples:

```java
serviceDiscovery, Serviceinstance, LRUCacheFactory
```

#### lowerCamelCase

**Method names, parameter names, member variables, and local variables should use lowerCamelCase.**

Correct examples:

```java
getUser Info()
createCustomThreadPool()
setNameFormat(String nameFormat)
User vice userService;
```

Incorrect examples:

```java
GetUser Info(), CreateCustomThreadPool(), setNameFormat(String NameFormat)
User vice user_service
```

### snake_case

**Test method names, constants, and enum names should use snake_case.**

In snake
