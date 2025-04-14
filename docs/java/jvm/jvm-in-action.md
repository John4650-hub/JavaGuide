---
title: JVM Online Issue Diagnosis and Performance Optimization Cases
category: Java
tag:
  - JVM
---

Diagnosing online JVM issues and performance tuning is also a common interview question, especially in the interviews of large companies for external hires.

In this article, I will share some related cases that I have come across.

Below is the main content.

[Analysis of an Online OOM Issue - Ai Xiaoxian - 2023](https://juejin.cn/post/7205141492264976445)

- **Phenomenon**: A certain online service has a very slow interface. Through monitoring, it was found that there is a significant GAP time, while the actual interface did not consume much time, and there were many such requests during that period.
- **Analysis**: Used the JDK's built-in `jvisualvm` to analyze the dump file (MAT can also analyze).
- **Recommendation**: For SQL statements, if a full table query without a `where` condition is detected, it should be defaulted to have a suitable `limit` added to prevent such issues from overwhelming the entire system.
- **Resources**: [Practical Case: A Journey of Analyzing a Dump File - HeapDump - 2022](https://heapdump.cn/article/3489050).

[Production Incident - A Special OOM Troubleshooting - Cheng Yu You Yun - 2023](https://www.cnblogs.com/mylibs/p/production-accident-0002.html)

- **Phenomenon**: Without any network issues, a certain open interface of the system became inaccessible and unusable starting from around 14:00 on March 10, 2023.
- **Temporary Solution**: Urgently rolled back to the previous stable version.
- **Analysis**: Used the MAT (Memory Analyzer Tool) to analyze the dump file.
- **Recommendation**: Under normal circumstances, the `-Xmn` parameter (which controls the size of the Young area) should always be less than the `-Xmx` parameter (which controls the maximum heap memory size), otherwise it will trigger OOM errors.
- **Resources**: [Summary of the Most Important JVM Parameters - JavaGuide - 2023](https://javaguide.cn/java/jvm/jvm-parameters-intro.html)

[Analysis of Large JVM Native Memory Leak (64M Issue) - Juejin - 2022](https://juejin.cn/post/7078624931826794503)

- **Phenomenon**: After the online project was just started, using the top command showed that RES occupied more than 1.5G.
- **Analysis**: The entire analysis process involved a lot of work, which can be followed step by step according to the author's thoughts, worth learning from.
- **Recommendation**: Stay away from Hibernate.
- **Resources**: [Memory Related Fields in Linux Top Command (VIRT, RES, SHR, CODE, DATA)](https://liam.page/2020/07/17/memory-stat-in-TOP/)

[YGC Issue Diagnosis, Which Enlightened Me Again! - IT Career Advancement - 2021](https://www.heapdump.cn/article/1661497)

- **Phenomenon**: After launching a new version, the advertising service received a large number of service timeout alerts.
- **Analysis**: Used the MAT (Memory Analyzer Tool) to analyze the dump file.
- **Recommendation**: Learn the troubleshooting thoughts for YGC (Young GC) issues and master the relevant knowledge points of YGC.

[I Heard JVM Performance Optimization is Difficult? Today I Tried it Out! - Chen Shuyi - 2021](https://shuyi.tech/archives/have-a-try-in-jvm-combat)

By observing the GC frequency and pause time, I adjusted the JVM memory space to achieve the most reasonable state. Remember to adjust in small steps to avoid severe memory fluctuations that could impact online services. This is actually the simplest way of JVM performance tuning and can be considered a rough tuning.

[Here Comes the Online GC Problem Cases You Wanted - Bianle Ge Cheng - 2021](https://mp.weixin.qq.com/s/df1uxHWUXzhErxW1sZ6OvQ)

- **Case 1**: Using Guava cache without setting a maximum cache size and weak references, leading to frequent Young GC triggers.
- **Case 2**: For a SQL query that sorts and paginates, and requires joining multiple tables, directly calling the SQL performs poorly under horizontal sharding. Thus, querying a single table, followed by sorting and pagination in memory using a List to hold the data, caused issues due to large data volume.

[Analysis and Solutions to 9 Common CMS GC Issues in Java - Meituan Tech Team - 2020](https://tech.meituan.com/2020/11/12/java-9-cms-gc.html)

This article has over 20,000 words, detailing GC basics and summarizing some common issues and solutions for CMS GC.

[Performed Some GC Tuning on an Inherited System, Reducing Pause Time by 90% - JD Cloud Technology Team - 2023](https://juejin.cn/post/7311623433817571365)

This article discusses a GC (Garbage Collection) issue encountered in a rules engine system, primarily manifested as a long Young GC (young generation garbage collection) after system startup, leading to performance degradation. After analysis, the core of the problem lay in the dynamic object age determination mechanism, which caused premature object promotion, resulting in prolonged garbage collection.
