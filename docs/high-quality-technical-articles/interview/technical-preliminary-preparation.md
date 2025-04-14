---
title: How to Prepare for Technical Interviews from the Perspectives of Interviewers and Candidates
category: Selected Technical Articles
author: Qinshi Yu
tag:
  - Interview
---

> **Recommendation**: This article explores technical interviews from both the interviewer and interviewee perspectives! Very insightful!
>
> **Content Overview:**
>
> - Assessing candidates through technical fundamentals allows for a true evaluation of their technical strength: depth and breadth.
> - Combining practical experience with theory. For example, after a candidate describes the JVM memory model layout, you can follow up with: What could cause an OOM? What preventive measures are there? Have you encountered memory leak issues? How did you troubleshoot and resolve such problems?
> - Limit project experience assessments to no more than two. This is because delving into the details of a project can be time-consuming. Generally, candidates are asked to choose a project they found most rewarding/challenging/memorable/interesting, and questions are centered around that project. Typically, this starts with the project background, assessing the technology stack, overall understanding of project modules and interactions, challenging technical issues encountered and their solutions, troubleshooting and resolving issues, code maintainability, and engineering quality assurance.
> - Ask more and say less, allowing candidates to showcase their abilities. Appropriately guide or progress based on the candidate's responses.

**Original Article Link:** <https://www.cnblogs.com/lovesqcc/p/15169365.html>

## Assessment Goals and Approach

First, clarify the assessment goals of the technical interview:

- The candidate's technical foundation;
- The candidate's problem-solving thought process and abilities.

Technical foundation is the cornerstone (the part below the iceberg), accounting for seventy percent, while problem-solving thought process and abilities are the visible part (the part above the iceberg), accounting for thirty percent. The assessment of business and technical foundations is a 70-30 split.

## Technical Foundation Assessment

### Why Assess Technical Foundation?

The two most important technical thinking abilities for programmers are logical thinking and abstract design abilities. Logical thinking is foundational, while abstract design is advanced. Assessing technical foundation allows for the evaluation of both thinking abilities. Understanding basic technical concepts and their relationships assesses logical thinking; the ability to abstract business problems into technical issues and organize mappings reasonably assesses abstract design ability.

Most business problems can be abstracted into technical problems. In a sense, business problems are merely domain-specific expressions of technical issues.

Therefore, **assessing candidates through technical fundamentals allows for a true evaluation of their technical strength: depth and breadth.**

### How to Assess Technical Foundation?

How to assess technical foundation? Through effective, multi-faceted questioning patterns.

#### What is it - Why

"What is it?" assesses basic understanding of concepts, while "Why?" assesses the principles behind the implementation of those concepts.

For example: What is an index? How is an index implemented?

#### Guiding - Lateral Questioning - In-depth Questioning

Guiding questions, such as "Are you familiar with Java synchronization tools?" serve as a probe. After receiving a positive response, you can further ask: "Which synchronization tool classes are you familiar with?" to understand the candidate's breadth.

After obtaining the candidate's response, you can further ask: "Can you discuss the implementation principles of `ConcurrentHashMap` or `AQS`?"

The extent to which a person can clearly articulate technical principles, including thoughts and details, indicates their mastery of the technology.

#### Jumping/Cross-Questioning

For example, when discussing efficient hash lookups, you can talk about hash consistency algorithms. The two are related yet have many differences, serving as a method to assess technical breadth.

#### Summary Questions

For example: What experiences have you gained from doing XXX that you can share? This assesses the candidate's ability to summarize and synthesize.

#### Combining Practical and Theoretical Knowledge

For example, after a candidate describes the JVM memory model layout, you can follow up with: What could cause an OOM? What preventive measures are there? Have you encountered memory leak issues? How did you troubleshoot and resolve such problems?

If a candidate discusses SQL optimization and index optimization, you can then discuss the implementation principles of indexes and how to establish the best indexes.

If a candidate mentions transactions, you can then discuss the principles of transaction implementation, isolation levels, snapshot implementations, etc.

#### Combining Familiar and Unfamiliar Topics

Ask about both the familiar parts listed on the candidate's resume and those not mentioned. For example, if the candidate's resume states they are familiar with the JVM memory model, you can assess memory management (the familiar part) and then probe into Java concurrency tool classes (the uncertain part).

#### Combining Dead Knowledge and Living Knowledge

For example, what search algorithms are there? Sequential search, binary search, hash search. These are typically easy to name and are considered "dead knowledge."

What scenarios are each of these search algorithms suitable for? In your work, what scenarios have you used which search algorithms? Why? These are "living knowledge
