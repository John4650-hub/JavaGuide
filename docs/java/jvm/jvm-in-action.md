I don't...
Title: JVM Online Problem-Checking and Performance-Reducing Cases
Category:
Tag:

- JVM
  I don't...

JVM online problem-checking and performance management are also frequently asked questions in interviews, especially in large and medium-sized companies.

In this article, I'll share some of the relevant cases I saw.

Here's the text.

[A Live OOM Problem Analysis - Ei Xiaoxien - 2023](https://juejin.cn/post/7205144922646445)

- **Phenomenon**: A service online has a very slow interface. Looking through a surveillance link, it was found that the middle GAP is very long, that the actual interface does not consume much time, and that there were many requests during that time.
- **Analysis**: Using JDK `jvisualvm` to analyze the dump file (MAT can also analyze).
- **Recommendation**: For SQL statements, if full-form queries without `where` conditions are monitored, a suitable `limit` should be implicitly added to prevent such problems from dragging down the system.
- **Info**: [Practical Case: Recording a Dump Document Analysis Course - HeapDump - 2022](https://heapdump.cn/article/348950).

[Purpose Accident - Recording a Special OOM Quest - A Cloud - 2023](https://www.cnblogs.com/mylibs/p/production-accent-0002.html)

- **Phenomenon**: In the absence of network problems, an open interface of the system could not be accessed and used from around 1400 hours on March 10, 2023.
- **Provisional Solution**: Emergency rollback to the previous stable version.
- **Analysis**: Analysis of dump files using MAT (Memory Analyzer Tool).
- **Recommendation**: `-Xmn` parameters (control the size of the Young area) should normally be smaller than `-Xmx` parameters (control the maximum size of the memory of the stack); otherwise, OOM errors will be triggered.
- **Info**: [Mainest JVM Parameters Summary - JavaGuide - 2023](https://javaguide.cn/java/jvm/jvm-parameters-intro.html)

[A Large Volume of JVM Native Memory Leaks (64M Problem) - Gold - 2022](https://juejin.cn/post/7078624931826794503)

- **Phenomenon**: Online projects have just started using top commands to see the RES occupy more than 1.5G.
- **Analysis**: The whole analysis process has been used for more work and can be followed by the author in one step, which is worth learning.
- **Recommendation**: Stay away from Hibernate.
- **Info**: Memory Related Fields (VIRT, RES, SHR, CODE, DATA) in [Linux Top Command](https://liam.page/1220/07/17/Memory-stat-in-TOP/)

[YGC # Question Question Question # Made Me Stand Again! - The Career Progression of IT People - 2021](https://www.heapdump.cn/article1661497)

- **Phenomenon**: The advertising service received a large number of service overtime warnings after the new version came online.
- **Analysis**: Using MAT (Memory Analyzer Tool) to analyze dump files.
- **Recommendation**: Learning to follow up on YGC issues and learning about YGC.

[Heard That JVM Performance Optimization Is Difficult? Today I Tried a Little! - Chen Shui - 2021](https://shuyi.tech/archives/have-a-try-in-jvm-combat)

JVM memory space adjustments are performed by observing GC frequencies and standstill time to achieve the most rational state. The adjustment process will keep in mind small paces and speeds to avoid the impact of RAM fluctuations on online services. It's actually the simplest kind of JVM eugenics, kind of crude.

[Your Online GC Problem Case Is Here - A Program - 2021]

- **Case 1**: Maximum caches and weak references were not set when Guava Cache was used, resulting in frequent triggers of Young GC.
- **Case 2**: For a query and sorting of SQL, this SQL requires more than one join table, under the partition table, to call directly SQL in poor condition. So, the sheet, then the memory sorting page, uses a list to save the data, and some of the data is big, and this is the phenomenon.

[9 Common CMS GC Problem Analysis and Resolution - Mission Mission - 2020](https://tech.meituan.com-2020/11/12/java-9-cms-gc.html)

The article, 2w+, details the basis of the GC and summarizes the analysis and solutions to some of the common problems of the CMS GC.

(https://juejin.cn/post/713116234338175365)

The article refers to a GC (waste recycling) problem encountered in the rule engine system, mainly as a
