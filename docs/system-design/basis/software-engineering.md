I don't...
title: Software Engineering Concise
Category: System design
I don't...

Most software developers ignore some of the most basic and bottom-up concepts of software development. However, these software development concepts are very important for software development, as if they were the cornerstone of software development. That's why I wrote this article.

# What's software engineering?

In 1968, NATO (North Atlantic Treaty Organization) introduced the term **software crisis** (**Software Chris**). In the same year, in order to address the software crisis, the concept of “**software engineering**” was born. A subject called software engineering has also emerged.

Over time, the subject of software engineering has also undergone a series of refinements, with some core elements, such as software development models, becoming more and more operational!

**What's a software crisis?**

In short, the software crisis described a painful point in software development at the time: it was very difficult to efficiently develop high-quality software.

Dijkstra also raised the software crisis in the 1972 Turing Prize award speech, in which he said: "The main reason for the software crisis is that the machine has become powerful in several orders of magnitude! Frankly, as long as there is no machine, there is no problem with programming. When we have small, weak computers, programming becomes a mild problem, and now we have huge computers, and programming is a huge problem as well.”

**What's a software project?**

The project is intended to solve practical problems by applying theory to practice. Software engineering refers to the application of engineering ideas to software development.

This is my definition of software engineering. Let's look at the more authoritative definition. The IEEE software engineering journal gives a definition of the following: (1) application of systematic, standardized, quantifiable methods to software development, operation, and maintenance, and application of engineering methods to software. (2) A study of the methods described in (1).

In short, the ultimate goal of software engineering is to create better and more easily maintained software with less resource consumption.\*\*

# Software development process

[Wiki encyclopedia defines the software development process in this way](https://zh.wikipedia.org/wiki/%E8%BDE%EF%E4%EB%B6%E5%EC%E5%E5%E5%E91%E8%E8%EF%87%E7%A8%25B):

> Software development process (English: software development process) or software process (English: software development process) is the development life cycle of software development (software development life cycle), with all phases of software definition and analysis, design, realization, testing, delivery, and maintenance. The software process is a step to be followed in developing and constructing the system and a roadmap for software development.

- Needs analysis: analysis of user needs and establishment of logical models.
- Software design: design of the software architecture based on the results of the needs analysis.
- Encoding: the source code for the program to run.
- Testing: Identify test examples and prepare test reports.
- Delivery: good software delivered to clients.
- Maintenance: maintenance of software such as solving bugs and improvement of functionality.

The software development process is only at a more general level, defining some of the processes that software development may involve.

The software development model defines the software development process more specifically and provides strong theoretical support for the development process.

# Software development model

There are many models for software development, such as Waterfall Model, Rapid Prototype Model, V-Model, W-Model, and Agility Development Model. The most representative of these are the **Waterfall model** and **Agility development**.

The **Waterfall model** defines a completed software development cycle and presents a complete picture of the life cycle of software.

![Waterfall Model](https://oss.javaguide.cn/github/javaguide/system-design/schedule-task/up-264f2750a3d30366ec375ec30ec2775.png)

**Agility development model** is the most currently used software development model. [Agile development](https://wiki.mbalib.com/wiki/%E6%E6%E8D%B7%E5%BC%80%E5%E8F%91) is a people-centered, iterative, step-by-step approach to development. In agile development, software project construction is cut into sub-projects, and the results of each sub-project are tested with integrated and operational features. In other words, a large project is divided into small, interconnected projects that can also run independently and be completed separately, in which the software has been in serviceable.

Some of the concepts that are more common now, such as **ongoing integration**, **re-engineering**, **small releases**, **low-documentation**, **pair programming**, and **test-driven development** are at the heart of agile development.

# Basic policy for software development

Software reuse

When we build new software, we do not need to start from scratch, and we can build software that meets requirements faster by reusing existing wheels (frameworks, third-party
