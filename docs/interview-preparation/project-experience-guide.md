---
title: Project Experience Guide
category: Interview Preparation
icon: project
---

::: tip Friendly Reminder
This article is excerpted from **[《Java Interview Guide》](../zhuanlan/java-mian-shi-zhi-bei.md)**. It is a column that teaches you how to prepare for interviews more efficiently, complementing the content of JavaGuide, covering common interview topics (system design, common frameworks, distributed systems, high concurrency, etc.), and high-quality interview experiences.
:::

## What if I don't have project experience?

Not having project experience is a common issue faced by most fresh graduates. In fact, many experienced programmers are also dissatisfied with the projects they have worked on in their companies and wish to find more technically challenging projects to work on.

Here are several reliable ways to gain project experience that I hope will inspire you.

### Practical Project Videos/Columns

Find a practical project video or column online that matches your abilities and job search requirements, and work on it alongside an instructor.

You can find suitable practical project videos/columns through platforms such as Mooc, Bilibili, Lagou, Geek Time, training institutions (like Black Horse, Silicon Valley), etc.

![Mooc Practical Course](https://oss.javaguide.cn/javamianshizhibei/mukewangzhiazhanke.png)

Try to choose a project that suits you; there is no need to strictly work on distributed/microservices projects. For the vast majority of students, it is already commendable to do well on a standalone project.

I have interviewed many candidates who claimed to have microservices project experience based on their resumes, but after asking a couple of questions, it was clear they didn't actually work on it or didn't think about it seriously during that time. This leaves a very negative impression on me.

In the "Interview Preparation" section of **[《Java Interview Guide》](../zhuanlan/java-mian-shi-zhi-bei.md)**, I also mentioned:

> I personally believe there is no need to focus on microservices or distributed projects, as they may not necessarily benefit your interview. The knowledge involved in microservices or distributed projects is too extensive, making it difficult for an average person to fully grasp. Moreover, such projects may be slightly beyond the scope for recent graduates. Even if you complete them, many interviewers may not consider them as entirely your independent work.
>
> In fact, doing an excellent job on a monolithic project is also very good, and can improve your personal skills just as well as tackling microservices or distributed projects. How to excel at it? Without even mentioning code quality, it’s more important to ensure your project has some highlights (like how you improved project performance or solved a specific pain point in the project). Try to quantify the achievements gained from your project experience, for example, "I used xxx technology to solve xxx problem, and the system's QPS increased from xxx to xxx."

While following the instructor's guidance, you must have your own thoughts and not just skim the surface. For many knowledge points, others' explanations may only be sufficient to complete the project, but if you want to deepen your understanding of important knowledge, you need to learn more independently.

### Practical Open Source Projects

There are many practical category projects on GitHub or Gitee; you can choose one to study. To gain a better understanding of the project, you can improve or add features to the original project based on your comprehension of its code.

You can refer to the [High-Quality Open Source Practical Projects](https://javaguide.cn/open-source-project/practical-project.html "High-Quality Open Source Practical Projects") for recommended practical open source projects, which are of high quality, covering a wide range of project types such as blog/forum systems, exam/question practice systems, e-commerce systems, permission management systems, rapid development scaffolds, and various libraries.

![High-Quality Open Source Practical Projects](https://oss.javaguide.cn/javamianshizhibei/javaguide-practical-project.png)

Always remember: **It’s not only about doing but also about improving and enhancing. Whether it’s practical project videos/columns or practical open source projects, there will always be many areas to完善和改进**.

### Start from Scratch

Take the initiative to create something you want to accomplish, and learn what you don’t know as you go along.

This approach has higher requirements, and I suggest you adopt it after you have some project experience. If you have never worked on a project before, it’s better to stick to the two methods mentioned above.

### Participate in Various Competitions Organized by Big Companies

If you can win awards in such competitions, the project will carry significant weight. Even if you don’t win, you can still include it on your resume.

![Aliyun Tianchi Competition](https://oss.javaguide.cn/xingqiu/up-673f598477242691900a1e72c5d8b26df2c.png)

### Participate in Actual Projects

Typically, you can access actual project development in enterprises through the following means:

1. Projects contracted by teachers;
1. Side projects you take on;
1. Projects encountered during internships/jobs;

Projects contracted by teachers and side projects often focus more on business aspects and rarely involve performance optimization. In such cases, consider improving the project; don’t hesitate to spend time on it, as it's important to dedicate yourself to doing one thing well over a certain period, like improving the project's data model or introducing caching to enhance access speed.

The projects you encounter during internships/jobs are similar; if you face more business-oriented projects, you should also privately work on improving and optimizing them.

Strive to genuinely optimize the project, as this itself enhances your personal skill set. If you really don’t have time for practice, that’s fine; just understand the optimization techniques for this project and prepare for common interview questions in advance.

## Are there any recommended projects?

In the "Interview Preparation" section of **[《Java Interview Guide》](../zhuanlan/java-mian-shi-zhi-bei.md)**, there is an article specifically compiling a list of high-quality practical projects that are very suitable for learning or as project experience.

![](https://oss.javaguide.cn/javamianshizhibei/project-experience-guide.png)

This article recommends a total of over 15 practical projects, including business-related projects and library projects, open source projects as well as video tutorials. For those participating in campus recruitment, I would particularly recommend working on one business project and one library project.

## Will the interviewer look down on projects I did by following videos?

Many fresh graduates create projects by following videos, and most interviewers are well aware of this.

It's possible that some interviewers don't appreciate this approach, but that typically depends on the person. However, I believe that most interviewers can understand this, given that students often lack avenues to gain practical project experience during their studies.

Most fresh graduates' project experiences are either self-found online or similar to yours, where they buy paid courses and follow along; only a small portion is from genuine projects. That you are willing to create a practical project speaks to a good intention, and you can indeed learn invaluable things from it. However, the crucial point is how much you master. The worst mistake when watching videos is to passively accept the content; instead, try to make your modifications and think critically! Even if you worked on a project by following a video, it can still be optimized!

**If you truly want to learn, I suggest that you not only focus on simply completing the project to run but also attempt to optimize it yourself!**

Here are a few relatively easy optimization points:

1. **Global Exception Handling**: Many projects don't handle this aspect very well; you can refer to my article: [《Using Enums to Simply Encapsulate an Elegant Spring Boot Global Exception Handling!》](https://mp.weixin.qq.com/s/Y4Q4yWRqKG_lw0GLUsY2qw) for optimization ideas.
1. **Project Technology Stack Optimization**: For instance, replace the local cache used with Guava with **Caffeine**. Caffeine offers better performance overall! Also, consider whether too much business logic is put in the Controller layer.
1. **Database Considerations**: Can the database design be optimized? Are indexes used correctly? Can SQL statements be optimized? Is it necessary to perform read-write separation?
1. **Caching**: Are there data points in the project that are frequently accessed? Has caching been introduced to improve response speed?
1. **Security**: Are there any security issues in the project?
1. ……

Additionally, I have shared common performance optimization practices in my community, covering areas such as multi-threading, asynchronous operations, indexing, and caching. I strongly recommend you take a look: <https://t.zsxq.com/06EqfeMZZ>.

Finally, **here’s a useful tip for optimizing code in IDEA—super handy!**

Analyze your code: Right-click the project -> Analyze -> Inspect Code

![](https://oss.javaguide.cn/xingqiu/up-651672bce128025a135c1536cd5dc00532e.png)

After the scan is complete, IDEA will suggest some potential code smells like naming issues.

![](https://oss.javaguide.cn/xingqiu/up-05c83b319941995b07c8020fddc57f26037.png)

Moreover, you can customize the inspection rules.

![](https://oss.javaguide.cn/xingqiu/up-6b618ad3bad0bc3f76e6066d90c8cd2f255.png)
