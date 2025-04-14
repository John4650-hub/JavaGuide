---
title: Code Refactoring Guide
category: Code Quality
---

Recently, I reread [“Refactoring: Improving the Design of Existing Code”](https://book.douban.com/subject/30468597/) and gained a lot from it. So, I wrote a simple article to share my views on refactoring.

![](https://oss.javaguide.cn/github/javaguide/image-20220311155746549.png)

## What is Refactoring?

A must-read book on refactoring, “Refactoring: Improving the Design of Existing Code,” provides definitions of refactoring from two perspectives:

> - Refactoring (noun): An adjustment to the internal structure of software, aimed at improving its understandability and reducing modification costs without changing its observable behavior.
> - Refactoring (verb): Using a series of refactoring techniques to adjust the structure of software without changing its observable behavior.

In more engineer-friendly terms: **Refactoring is using design patterns (such as Composite, Strategy, and Chain of Responsibility patterns), software design principles (such as SOLID principles, YAGNI principle, KISS principle), and refactoring techniques (such as encapsulation, inheritance, and building a testing system) to make code easier to understand and modify.**

Software design principles guide us in organizing and standardizing code, while refactoring aims to design software that meets these principles as much as possible.

The core of correct refactoring is that **the steps must be small; each refactoring step should not affect the normal operation of the software, and refactoring can be stopped at any time.**

**Common design patterns include**:

![Common Design Patterns](https://oss.javaguide.cn/github/javaguide/system-design/basis/common-design-patterns.png)

For a more comprehensive summary of design patterns, refer to the **[java-design-patterns](https://github.com/iluwatar/java-design-patterns)** open-source project.

**Common software design principles include**:

![Common Software Design Principles](https://oss.javaguide.cn/github/javaguide/system-design/basis/programming-principles.png)

For a more comprehensive summary of design principles, refer to the **[java-design-patterns](https://github.com/iluwatar/java-design-patterns)** and **[hacker-laws-zh](https://github.com/nusr/hacker-laws-zh)** open-source projects.

## Why Refactor?

When introducing the definition of refactoring above, I discussed its benefits from a more abstract perspective: the main purpose of refactoring is to enhance the flexibility, scalability, and reusability of code and architecture.

What specific benefits can refactoring bring to a real project?

1. **Make code easier to understand**: By adding comments, following naming conventions, and optimizing logic, we can make our code more understandable.
1. **Avoid code rot**: Refactoring helps eliminate bad-smelling code.
1. **Deepen understanding of the code**: The process of refactoring code deepens your understanding of certain parts of the code.
1. **Discover potential bugs**: Many potential bugs are discovered during the refactoring process.
1. ……

After reviewing the benefits of refactoring mentioned above, you will find that the ultimate goal of refactoring is to **improve the speed and quality of software development**.

Refactoring does not slow down software development; on the contrary, if code quality and software design are poor, the development speed will slow down when we want to add new features. Eventually, there may even be an urge to rewrite the entire system.

![](https://oss.javaguide.cn/github/javaguide/bad&good-design.png)

In “Refactoring: Improving the Design of Existing Code,” it is stated:

> The sole purpose of refactoring is to allow us to develop faster and create greater value with less effort.

## Is Performance Optimization Refactoring?

The purpose of refactoring is to improve code readability, maintainability, and flexibility. It focuses on the internal structure of the code—how to make it easier for developers to understand the code and how to make subsequent feature development and maintenance more efficient. Performance optimization, on the other hand, aims to make code run faster and consume fewer resources. It focuses on the external performance of the program—how to reduce response time, lower resource consumption, and improve system throughput. Although these two may seem opposing, their goals are unified: both aim to improve the overall quality of the software.

In actual development, the ideal approach is to first **ensure code readability and maintainability**, and then choose appropriate performance optimization techniques based on actual needs. Excellent software design does not blindly pursue maximum performance but seeks to find a balance between maintainability and performance. This way, we can create software systems that are both **easy to manage** and have **good performance**.

## When to Refactor?

Refactoring can be done at any time during the development process; it should be done as needed and does not require setting aside one or two days specifically for
