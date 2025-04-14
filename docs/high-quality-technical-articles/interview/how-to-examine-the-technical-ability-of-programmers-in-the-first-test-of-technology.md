---
title: How to Assess a Programmer's Technical Ability in Technical Interviews
category: Selected Technical Articles
author: Qinshi Yuyu
tag:
  - Interview
---

> **Recommendation**: This article explores technical interviews from both the interviewer and interviewee perspectives! Very insightful!
>
> **Content Overview:**
>
> - Combine practical experience with theory. For example, after a candidate describes the JVM memory model layout, you can follow up with questions like: What could cause an OOM? What preventive measures can be taken? Have you encountered memory leak issues? How do you troubleshoot and resolve such problems?
> - Limit the assessment of project experience to no more than two. This is because delving into the details of a project takes considerable time. Generally, candidates are asked to choose a project they found most rewarding, challenging, memorable, or particularly interesting. Questions will then revolve around this project, typically starting from the project background, assessing the technology stack, overall understanding of project modules and interactions, challenging technical issues encountered and their solutions, troubleshooting and problem-solving, code maintainability, and engineering quality assurance.
> - Ask more and say less, allowing candidates to showcase themselves. Appropriately guide or progress based on the candidate's responses.
>
> **Original Article Link**: <https://www.cnblogs.com/lovesqcc/p/15169365.html>

## Soulful Three Questions

1. What do you think of the person? [Expression ability, communication ability, learning ability, summarization ability, self-reflection and improvement ability, stress resistance, emotional management ability, influence, team management ability]
1. If asked to independently complete the design and implementation of a project, do you think they can handle it? [System design ability, project management ability]
1. What is your evaluation of their analysis and problem-solving abilities? [Understanding of principles, practical application ability]

## Assessment Goals and Ideas

First, clarify the assessment goals of the technical interview:

- The candidate's technical foundation;
- The candidate's problem-solving thought process and abilities.

The technical foundation is the cornerstone (the part below the iceberg), accounting for seventy percent, while the problem-solving thought process and abilities are the visible part (the part above the iceberg), accounting for thirty percent. The assessment of business and technical foundations should be in a 70-30 ratio.

Core assessment goal: the ability to analyze and solve problems.

From a technical perspective: depth + application ability + breadth. For campus recruitment or social recruitment below P6 level, more emphasis should be placed on depth + application ability, with breadth as a bonus; above P6, breadth can be increased.

- Campus recruitment: solid foundation, agile thinking. Main assessment content: basic data structures and algorithms, processes and concurrency, memory management, system calls and IO mechanisms, network protocols, database paradigms and design, design patterns, design principles, programming habits;
- Social recruitment: rich experience, well-rounded. Main assessment content: a certain depth of foundational technical mechanisms, such as Java memory model and memory leaks, JVM mechanisms, class loading mechanisms, database indexing and query optimization, caching, message middleware, project, architecture design, engineering specifications, etc.

### What is Technical Foundation?

As a technical interviewer, how do you assess the technical foundation? What exactly is the technical foundation? Is it knowing what to do, or knowing how to think? Knowledge, as an existing mature principle system, constitutes an important part of the foundation, while knowing how to think is equally important. As the saying goes, "to know the why behind the what." Knowing the what means being familiar with the existing knowledge system, while knowing the why means deriving from the bottom up, truly understanding the origins of knowledge, and comprehending why it is this way and not that way. After all, in the logical world of programming, there are no fixed rules. Knowing how to think, being able to design and develop meticulously, and delving into details—that is the technical foundation.

### Why Assess Technical Foundation?

The two most important technical thinking abilities for programmers are logical thinking ability and abstract design ability. Logical thinking ability is foundational, while abstract design ability is advanced. Assessing the technical foundation allows for the simultaneous assessment of these two thinking abilities. The ability to understand foundational technical concepts and their relationships assesses logical thinking ability; the ability to abstract business problems into technical problems and organize the mapping reasonably assesses abstract design ability.

The vast majority of business problems can be abstracted into technical problems. In a sense, business problems are merely domain-specific expressions of technical problems.

Therefore, assessing candidates through their technical foundation allows for a true evaluation of their technical strength: depth and breadth.

### Why Not Only Assess Business Dimensions?

Because candidates are usually quite familiar with business aspects, they may simply regurgitate existing solutions, making it difficult to assess their deep understanding, lateral expansion, and summarization abilities.

In this regard, it is advisable to specifically assess the candidate's summarization ability: for example, during the process of building, developing, or maintaining microservices
