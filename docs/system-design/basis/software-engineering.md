---
title: A Concise Guide to Software Engineering
category: System Design
---

Most software development practitioners tend to overlook some of the most fundamental and basic concepts in software development. However, these concepts are crucial for software development, serving as the foundation of the field. This is also the reason I wrote this article.

## What is Software Engineering?

In 1968, NATO introduced the term **Software Crisis**. In the same year, the concept of **Software Engineering** was born to address the software crisis. A discipline called software engineering emerged as a result.

Over time, the discipline of software engineering has undergone numerous refinements, with some core content, such as software development models, becoming increasingly rich and practical!

**What is the Software Crisis?**

In simple terms, the software crisis describes a pain point in software development at that time: it was difficult to efficiently develop high-quality software.

Dijkstra (the author of the Dijkstra algorithm) also mentioned the software crisis in his Turing Award acceptance speech in 1972, stating: "The main reason for the software crisis is that machines have become powerful by several orders of magnitude! Frankly, as long as there are no machines, programming is no problem at all. When we had some weak computers, programming became a mild problem, but now that we have large computers, programming has also become a huge problem."

**After all this, what exactly is Software Engineering?**

Engineering is the application of theory to practice to solve real-world problems. Software engineering refers to the application of engineering principles to software development.

The above is my definition of software engineering; now let's look at a more authoritative definition. The IEEE Software Engineering Journal defines it as follows: (1) The application of systematic, standardized, and quantifiable methods to the development, operation, and maintenance of software, i.e., applying engineering methods to software. (2) The study of the methods described in (1).

In summary, the ultimate goal of software engineering is to **create better and more maintainable software with less resource consumption.**

## Software Development Process

[Wikipedia defines the software development process](https://zh.wikipedia.org/wiki/%E8%BD%AF%E4%BB%B6%E5%BC%80%E5%8F%91%E8%BF%87%E7%A8%8B) as follows:

> The software development process, or software process, is the software development life cycle, where each phase implements the definition and analysis of software requirements, design, implementation, testing, delivery, and maintenance. The software process is a roadmap that should be followed when developing and building systems.

- Requirement Analysis: Analyze user needs and establish a logical model.
- Software Design: Design the software architecture based on the results of the requirement analysis.
- Coding: Write the source code that runs the program.
- Testing: Determine test cases and write test reports.
- Delivery: Deliver the completed software to the client.
- Maintenance: Maintain the software, such as fixing bugs and improving features.

The software development process is a relatively general overview that defines some processes that may be involved in software development.

Software development models define the software development process more specifically and provide strong theoretical support for the development process.

## Software Development Models

There are many types of software development models, such as the Waterfall Model, Rapid Prototype Model, V-Model, W-Model, and Agile Development Model. Among them, the most representative are the **Waterfall Model** and **Agile Development**.

The **Waterfall Model** defines a complete software development cycle, fully illustrating the lifecycle of a software product.

![](https://oss.javaguide.cn/github/javaguide/system-design/schedule-task/up-264f2750a3d30366e36c375ec3a30ec2775.png)

The **Agile Development Model** is currently the most widely used software development model. [MBA智库百科 describes Agile Development as follows](https://wiki.mbalib.com/wiki/%E6%95%8F%E6%8D%B7%E5%BC%80%E5%8F%91):

> **Agile Development** is a human-centered, iterative, and incremental development method. In Agile development, the construction of a software project is divided into multiple sub-projects, each of which is tested and has integration and runnable characteristics. In other words, a large project is divided into multiple interrelated but independently runnable small projects, which are completed separately, and the software remains usable throughout the process.

Common concepts such as **Continuous Integration**, **Refactoring**, **Small Releases**, **Low Documentation**, **Stand-up Meetings**, **Pair Programming**, and **Test-Driven Development** are all core aspects of Agile development.

## Basic Strategies for Software Development

### Software Reuse

When building new software, we do not need to start from scratch. By reusing existing components (frameworks, third-party libraries, etc.), design patterns, design principles, and other ready-made materials, we can build
