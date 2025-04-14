---
title: A Four-Year Work Summary from Campus Recruitment to Tencent
category: Selected Technical Articles
author: pioneeryi
tag:
  - Personal Experience
---

The profession of a programmer has a high turnover rate, with new faces often arriving and familiar ones leaving, some departing voluntarily while others are laid off.

Moreover, the competition has intensified in recent years, leading to a greater workload but less reward, making the internet industry seem less appealing than before.

The constant ebb and flow of people and the unpredictability of circumstances have become something I am accustomed to.

The only way for employees to succeed is to refine their professional skills and enhance their core competitiveness, so that no matter what changes occur, they will always be able to make a living.

Today, I want to share the story of a blogger who joined Tencent through campus recruitment and left after four years of work.

As for why he left, I am unclear; it may be due to better opportunities or a perception that his current job offered limited personal growth.

**In the following text, "I" refers to this author.**

> Original link: <https://zhuanlan.zhihu.com/p/602517682>

After graduating from graduate school, I have been working at Tencent, and unconsciously, four years have passed. I personally do not have the habit of intentionally summarizing, as I have only focused on moving forward and forgot to pause and reflect. I remember reading a career planning document that mentioned the idea of three-year phases and five-year phases; right now, it just so happens to be four years, and since I'm leaving Tencent, it's time to summarize.

First, let's evaluate my four years simply: I believe that this time has not been completely wasted or squandered. Why do I say this? Because I have realized that comparing myself to others seems meaningless; there are many who are doing better than I am, and there are also quite a few who are not doing as well as I am. Ultimately, I am just an ordinary person, not remarkable, and my skills do not stand out. Accepting my ordinariness and checking whether I am satisfied with what I have done is what matters.

Now, let's talk about a few specific points; I mainly want to discuss work, performance, EPC, core culture, and finally my gains.

## Work Situation

I have not changed roles within Tencent, but I have worked on a variety of projects, including: BUGLY, Distributed Call Chain (Huskie), Crowdsourcing System (SOHO), and EPC Measurement System. Some are external projects, while others are internal systems; perhaps some of you do not know about them. I am quite grateful for these project experiences, as they encompass both pure business systems and framework-oriented systems, allowing me to learn a lot.

Next, let me briefly introduce each project, as each one has absorbed a lot of effort:

BUGLY is a terminal crash reporting system that many apps have integrated. Huskie is a distributed call chain tracing project built on Zipkin. SOHO is a crowdsourcing system primarily for outsourcing data standards and voice collection tasks. The EPC Measurement System is a system for measuring development efficiency. Here, I’d like to share my understanding and insights on business development. Many people may have doubts like I initially did—how does one grow while doing business development? In other words, how can one grow while constantly performing CRUD operations? I also had such doubts at first, but later I changed my perspective.

I believe that the complexity of a system can be roughly divided into technical complexity and business complexity. For business systems, business complexity is higher, while for framework systems, technical complexity is higher. Tackling both types of complexity presents significant challenges.

The crowdsourcing system I previously worked on involved various business logics, which is indicative of high business complexity. To address this problem, we began to explore and practice Domain-Driven Design (DDD), which indeed provided some assistance and prevented the system from becoming overly chaotic. During this process, my insights into DDD have influenced my later project organization, design, and development.

Of course, DDD is not a silver bullet, and I am not claiming it to be exceptional; it’s just that after understanding it, I can occasionally approach design and development from a different perspective.

I have realized that excelling in business is not as easy as one might think. If we can explore more and practice while introducing good methods, ideas, or architectures, it will benefit both the individual and the business.

## Performance Situation

During my four years at Tencent, assessments are conducted every six months, totaling eight assessments. Reflecting back, my performance ratings over four years were: three stars, three stars, five stars, three stars, five stars, four stars, four stars, and three stars. Statistics show that four and five-star ratings made up exactly half.

![](https://oss.javaguide.cn/github/javaguide/high-quality-technical-articles/640.png)

PS: Luckily, I had trophies in the past; otherwise, there would have been no motivation at all. (It seems Tencent doesn't hand them out anymore).

What sticks in my mind are two experiences of receiving five-star ratings. The first time was in my second year, while working on the crowdsourcing project. Because the project itself wasn't too challenging, I dedicated some energy to the fundamental construction of the team, helping to build Java and Golang project scaffolding and delivering several technical sharing sessions. Ultimately, my leader felt that I performed quite outstandingly and awarded me five stars. It appears that being proactive can benefit both oneself and the team, leading to rewards in return.

The second five-star experience was related to EPC. Here's a amusing anecdote: I learned later that during the project's early stages, when the director presented the system to the boss and it took a long time to load the indicators, the director awkwardly noted that they were still optimizing. After a while, during another presentation, it again took a long time to load, and the director helplessly claimed they were still optimizing. I didn’t expect that I had caused the director such embarrassment, haha. Well, let me explain the outcome: ultimately, I developed a query engine to replace Mondrian, and after that, such awkward situations never occurred again. This led to a good performance evaluation. Working on the EPC measurement project, I felt significant growth, particularly in stress resistance; when you build a system from scratch, there is a process of enduring before optimizing. Additionally, if your project is crucial, particularly related to data, every minor issue can keep you on edge, requiring you to think of ways to mitigate risks and failures. Another different feeling was that in previous projects, I was mostly a developer, while in this system, I was the owner and responsible person. Being the owner of a system demands continuous responsibility and requires constant thought about the system's planning and direction, as well as proper task allocation and progress control. This role experience was entirely different from before.

## Discussing EPC

Many people criticize EPC or laugh at it. As one of the core developers of the measurement platform, I would like to share my objective perspective.

In fact, the original intention of EPC is good. It aims to measure the quality of various aspects of development efficiency through comprehensive and multidimensional indicators, thereby enhancing business effectiveness. However, in the end, it became evident during implementation that the objective conditions do not support this (the tools are not yet well established). Moreover, the blind pursuit of indicator data led those underneath to find ways to make the indicators look good, ultimately contradicting the initial intention.

Why, when discussing EPC positively, the truth is that if you closely examine EPC, you will find that it is actually a rather complete and advanced set of measurement indicators. It covers requirements, code, defects, testing, continuous integration, and operational deployment across all aspects.

Additionally, while a few individuals and businesses have manipulated the system, the majority of businesses have made changes, such as feedback from the team at Weishi, where people noted that their previous code quality was poor, but after EPC was introduced, code quality improved significantly. Although Weishi ultimately failed, at that point, the situation was beyond saving, and we cannot blame EPC for its demise.

## Discussing Core Culture

Many people say that Tencent has a prevalent core culture. However, I think this is similar in any company. This aligns with the basic principle that people only trust those they know and feel familiar with. As a leader, would you hand over important tasks to someone you are not familiar with?

I actually don't know if I can be considered part of the core culture. Someone once asked on an online platform, "How do you know if you are part of the core culture?" One answer struck me as insightful: "If you don't know whether you are part of the core culture, then you probably aren't." Haha, by this logic, I may not be.

On the other hand, later on, I took on some very important responsibilities within the team, likely some of the most important within our center, where I independently managed a direction and reported directly to the director, which seems somewhat similar.

There are other viewpoints online as well, bluntly stating that whether you are part of the core culture hinges on monetary gains, which makes sense. When I was at level 7, I received stock options, and I felt pretty good about it. At the time, I thought that, barring any accidents, my future financial prospects and career development would be smooth sailing. But then surprises happened; the following year, EPC did not meet expectations, and the department's general manager and director were replaced, leading to a new director in our center.

Well, I had to rebuild trust again. Later on, whether one is part of the core culture became less significant, as with the economic downturn and layoffs, most people left, either voluntarily or involuntarily.

In summary, the existence of core culture is understandable. How does one become part of it? I honestly don’t know. However, I believe that rather than pondering how to join the core culture, it would be more productive to focus on how to demonstrate one’s value and capability. Once others recognize your value and capabilities, more opportunities will naturally come your way. When opportunities arise, just seize them, and there will be more benefits.

## Reflecting on Gains

What does it mean to gain? I believe that both external factors such as material rewards, skills, and levels, as well as internal insights and recognitions, count as gains.

First, I'll mention some quantifiable aspects that I believe I have achieved:

- In terms of level, I have risen to level nine, becoming a senior engineer. Although people say that Tencent has reduced level allocations, I actually know whether I possess the skills of a senior engineer. Personally, I feel I have reached the state that I needed to be in during my years of effort.
- As for performance, self-assessment shows that I am not someone particularly bent on competition, or rather, I won’t compete just for the sake of competition. However, if I believe I should do something well, my ownership mindset and sense of responsibility are still commendable. Ultimately, my performance at Tencent over the four years can be considered satisfactory.

Now, let's discuss some other soft skills:

**1. Documentation Skills**

As a programmer, documentation skills are actually quite important. Honestly, I don’t think my documentation skills are exceptional, but both of my previous directors have said my documentation was good, so it seems I might be above average.

**2. Clear Direction**

Lastly, I’d like to mention something less tangible but still valuable: I have gradually clarified or determined my future direction, which is to engage in data development.

In fact, finding and identifying a target is challenging, and few people around have clear goals and directions; most are in a state of confusion.

Not long ago, I was chatting with someone about career planning, and we discussed two perspectives to consider:

- Choose a business direction, such as e-commerce or advertising, and continuously accumulate knowledge and skills related to that business area. As experience accumulates, you will become an expert in that field.
- Delve into a technical direction and continuously research foundational technical knowledge, increasing your chances of becoming a specialist in that technology.

To be honest, while I have deeply researched and practiced Domain-Driven Design, employing it to model and solve complex business problems, my heartfelt preference leans towards delving into technology, and I am very interested in big data. Therefore, I have decided that my future direction will center around data-related work.

My four years at Tencent mark my first work experience, where I have met many outstanding individuals and learned a lot. In the end, my voluntary departure can also be seen as a dignified exit (even though I sacrificed a huge gift), and I am still grateful for Tencent.
