I don't...
title: Code Reconstructing Guide
Category: Code quality
I don't...

The previous period read [Reconstruction: Improvements in Designed Codes](https://book.douban.com/subject/30468597/) and received a large number of goods. So, a brief article was written to tell me what I thought about the remodeling.

![image](https://oss.javaguide.cn/github/javaguide/image-2020031111546549.png)

# What's a remodel?

A book entitled Reconstructing: Improving the Design of Codes, which learns to restructure, defines the reconstruction in two ways:

> - Reconstruct (noun): An adjustment to the internal structure of the software, which is intended to improve its comprehensibility and reduce the cost of its modification, without changing its observable behavior.
> - Reconstruct (verb): using a series of reconstructive techniques, restructuring the software without altering its observable behavior.

In a language more closely related to the engineer: **Reconstruction is the use of design models (e.g., combination model, strategy model, liability chain model), software design principles (e.g., SOLID principle, YAGNI principle, KISS principle), and reconstruction tools (e.g., sealing, inheritance, construction of test systems) to make codes more understandable and to make them easier to modify.**

Software design principles guide our organization and code of conduct, and at the same time, the re-engineering is designed to maximize the design of software that meets the design principles of software.

The core of the correct re-engineering is that **steps must be small, that each re-engineering does not affect the proper functioning of the software and can be stopped at any time.**

**Common design models are as follows:**

![common design model](https://oss.javaguide.cn/github/javaguide/system-design/basis/common-design-patterns.png)

A more comprehensive summary of design models can be found in **[java-design-patterns](https://github.com/iluwatar/java-design-patterns)**, this open-source project.

**Common software design principles are as follows:**

![programming principles](https://oss.javaguide.cn/github/javaguide/system-design/basis/programming-principles.png)

A more comprehensive summary of design principles can be found in **[java-design-patterns](https://github.com/iluwatar/java-design-patterns)** and **[hacker-laws-zh](https://github.com/nusr/hacker-laws-zh)**.

Why'd you recreate it?

In the context of the above recasting definition, I presented the advantages of the recasting in a more abstract way: The main purpose of the reorganization is to enhance the flexibility/extensibility and reuse of the code & architecture.

What benefit would it bring to us if it corresponded to a real project?

1. **To make codes more understandable**: to make our codes more understandable by adding notes, naming norms, and logical optimization;
1. **Avoiding coding**: Decoration of tasting code through re-engineering;
1. **Deepening the understanding of the code**: The process of reconstructing the code will deepen your understanding of a part of the code;
1. **Feed out potential bugs**: this is the case, many of the potential bugs were discovered during the re-engineering process;
1. ...

When you look at the benefits of the restructuring described above, you will find that the ultimate goal of the restructuring is **to increase the speed and quality of software development**.

Reengineering does not slow down the pace of software development; rather, if the quality of the code and the design of the software is poor, the pace of development is getting slower if we want to add new features. In the end, there was even an urge to rewrite the system.

![bad and good design](https://oss.javaguide.cn/github/javaguide/bad&good-design.png)

The book Reconstruct: Improving the Design of the Code says:

The only purpose of the re-engineering is to allow us to develop faster and to create greater value with less work.

Is performance optimization a re-engineering?

The purpose of the reorganization is to improve the readability, maintenance, and flexibility of the code, focusing on the internal structure of the code — how to make it easier for developers to understand the code and how to make subsequent functional development and maintenance more efficient. Performance optimization, on the other hand, is intended to allow the code to operate faster and with fewer resources, and is concerned with the external performance of the program - how to reduce response time, reduce resource consumption, and increase system throughput. The two appear to be in opposition, but in fact, their goal is to harmonize and to improve the overall quality of the software.

In practical development, it would be desirable to first ensure the readability and maintenance of codes \*\*and then to select the appropriate performance optimization tools according to actual needs. Good software design is not simply a quest to maximize performance, but rather
