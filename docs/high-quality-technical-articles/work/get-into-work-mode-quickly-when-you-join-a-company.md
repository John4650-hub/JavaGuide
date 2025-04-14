---
title: How to Quickly Enter Work Mode After Joining a New Company
category: Selected Technical Articles
tag:
  - Work
---

> **Recommendation**: It is highly recommended that every newcomer or existing employee read this article. It can help you avoid many pitfalls. The entire article is logically clear and comprehensive!
>
> **Original link**: <https://www.cnblogs.com/hunternet/p/14675348.html>

![How to Quickly Enter Work Mode After Joining a New Company](https://oss.javaguide.cn/github/javaguide/high-quality-technical-articles/work/%E6%96%B0%E5%85%A5%E8%81%8C%E4%B8%80%E5%AE%B6%E5%85%AC%E5%8F%B8%E5%A6%82%E4%BD%95%E5%BF%AB%E9%80%9F%E8%BF%9B%E5%85%A5%E7%8A%B6%E6%80%81.png)

The annual job-hopping season of "March Gold and April Silver" is about to come to an end. I believe many job-hoppers have already found their ideal positions and are about to or have already started anew.

For those with job-hopping experience, you know that each new company comes with potentially new business, new technologies, and new teams... These may disrupt your original working thought processes, coding habits, and collaboration methods...

For companies, they cannot give you months to get acclimatized. At this time, how to quickly enter work mode and quickly showcase your value is extremely important.

Some may be fortunate enough to join companies with well-established processes and mechanisms, allowing new employees to quickly integrate into work through mentoring, various training, etc. Others may not be so lucky; for example, when I switched jobs to a certain company a few years ago, there was no such complete mechanism for onboarding. I also happened to join during one of the busiest periods for the team, and on my first day, I was immediately given several issues to troubleshoot online without any documentation or training. Many people may find it hard to adapt quickly, leading them to feel overwhelmed by the pressure and consider leaving.

![bad175e3a380bea.](https://hunter-picgos.oss-cn-shanghai.aliyuncs.com/picgo/bad175e3a380bea..jpg)

So, **how can we quickly adapt ourselves to a new work mode and work rhythm?**

When starting a new job that involves a pile of code repositories, many people often feel lost. However, if we reflect on our past work and project experiences, we can find similarities. When starting a new project, we generally go through several steps: requirements -> design -> development -> testing -> release, and this cycle repeats as we complete project after project.

![Project Process](https://oss.javaguide.cn/github/javaguide/high-quality-technical-articles/work/image-20220704191430466.png)

In this process, there are four main aspects of knowledge that run through everything: business, technology, projects, and team. Our first stage goal when starting a new job is to have the ability to follow the team in project execution, so the knowledge we should quickly master should begin with these four aspects.

## Business

Many might think that as a tech person, the most important thing to understand is technology, right? And so, after joining a company, they eagerly dive into technical documentation, system architecture, or even start "gnawing" through the source code. If you’re doing this, you're making a big mistake! In almost all companies, technology serves as a tool. Although it’s important, it exists to support the business. Technology addresses the "how-to" aspect, while business tells us "what to do" and "why to do it." Once you detach from business, the existence of technology becomes meaningless.

To understand business, there are two very important methods:

**First is to ask.**

If you're in a team with a robust business training mechanism and detailed requirement documents, you might not need to ask too many questions to understand the business. However, this ideal situation is rare; most companies lack such conditions. Therefore, we can only rely on asking questions.

It's crucial for newcomers to be willing to ask questions without feeling embarrassed. I've seen many newcomers hesitate to ask due to being shy or introverted, which causes them to struggle to integrate into the team and take on responsibilities for a long time. Don’t fear asking questions — it's the fear of being scolded or rebuffed that is more detrimental. I believe the vast majority of programmers are quite approachable!

**Second is through testing.**

I believe that testing is definitely a great way for someone to quickly understand the team’s business. Through testing, we can walk through the entire process of the project our team is responsible for. If we encounter something we can’t figure out, we should timely ask questions. In this process, we naturally grasp the core business workflow quickly.

While understanding the business, we should focus on not getting too caught up in details. Our goal is to get a holistic understanding of the business process, the users we are targeting, and the services we provide…

## Technology

Once we have a preliminary understanding of the business, it’s time to delve into technology. You might be eager to start flipping through the source code, but hold on a minute.

At this point, we should consider how we would implement the system based on our understanding of the business and our past experiences. This step is essential as it allows us to compare the implementation approach against actual system technical implementations later. We can identify any differences, analyze why those differences exist, understand what works better, and what doesn’t. For the aspects that aren’t as effective, we can suggest improvements, and for those that are better, we can learn and integrate them into our own practice!

Next, we should explore the technology, but not by jumping straight into the source code. **We should analyze the system gradually from a macro to a micro perspective.**

First, we should get a simple overview of **the technology stack used by our team/project**, whether it’s Java, .NET, or a combination of languages. Is it a front-end and back-end separation, or does the server cover everything? What kind of database is used — MySQL, PostgreSQL? This way, we can set certain expectations regarding the technologies, frameworks, and our responsibilities, which some might have gathered even during the interview process.

Next, we should understand **the macro business architecture of the system**. What systems does our team primarily manage? What modules are each of these systems composed of? How do they interact with external systems? It’s ideal to document these in the form of flowcharts or mind maps.

Then, we need to look at **what external interfaces or services our team provides**. What functions does each interface and service offer? We can continue testing our system; at this stage, we should examine which pages are included in the main processes, what backend APIs are called by each page, and which code repositories correspond to each backend API. (If we are only providing backend services, we can look at what services we offer and which upstream services call them…). Again, this should be organized visually.

Next, we should understand **what external services our system or services rely on** — i.e., what external systems support this. These services might originate from outside the team or company, or they could come from other companies. At this point, we can look at the code and examine how interactions with external systems are set up, including communication frameworks (REST, RPC) and protocols.

When we reach the code layer, the first thing we should understand is the hierarchical structure of the code for each module — how many layers each module has, what the responsibility of each layer is. Understanding this gives us a preliminary concept of the system's entire design, followed by locating the code directory structure and configuration files.

Finally, we should look for an example — it could be an interface or a page — to follow the code's execution path through the inputs to outputs for a complete walkthrough to verify our previous understanding.

At this point, we can temporarily conclude our understanding of the technical aspects. Our aim is to gain a preliminary comprehension of the system; we’ll have plenty of time later to delve deeper into the details.

## Projects and Teams

As mentioned earlier, when starting at a new company, the first phase goal is to be capable of working on projects alongside the team. Next, we need to understand how projects operate.

We should capture some key points in the process from requirement design to code writing and ultimately to release and online deployment. For instance, does the project adopt an Agile or Waterfall model? What is the duration of an iteration cycle? Where do requirements come from, and how are they presented? Is there a requirement review process? What are the coding standards? How is the code structured? What regulations govern submission? How is testing delivered? What preparations are needed before release, and how are release tools utilized?

For understanding projects, simply observing colleagues or personally experiencing an iteration of development will provide a rough understanding.

While understanding project operations, we should also familiarize ourselves with the team. Again, we start from the outside. Which external teams are we interfacing with? For instance, where do requirements come from? Are there connections to external teams? What upstream teams provide our services, and what downstream teams rely on us? How do teams communicate, and what are the common communication methods?

Next, we focus on internal team dynamics. What roles exist within the team, and what are each person's responsibilities? This helps us identify whom to approach when issues arise. Are there any regular activities or meetings, such as daily stand-ups or weekly review meetings? Are there any conventional rules, internal reviews, or sharing mechanisms?

## Conclusion

When joining a new company and facing new work challenges, being able to quickly enter work mode and demonstrate your value will lead to a strong start.

As a programmer, quickly entering work mode means we should first have the ability to collaborate with the team on projects. From the perspective of backend development, I summarized some methods and experiences across four areas: business, technology, projects, and teams.

If you have good methods or suggestions on how to quickly enter work mode, feel free to share them in the comments.

Finally, let’s review the contents of this article with a mind map. If you find this article helpful, you can follow the WeChat account at the end of the article, where I will frequently share my experiences and insights gained throughout my growth journey for everyone to learn and improve together.

<!-- @include: @article-footer.snippet.md -->
