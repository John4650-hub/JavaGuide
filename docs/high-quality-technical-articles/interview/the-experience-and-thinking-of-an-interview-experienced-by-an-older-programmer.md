---
title: The Trials and Reflections of a Middle-aged Programmer in Interviews
category: Selected Technical Articles
author: Qin Shuiyu
tag:
  - Interview
---

> **Recommendation**: The author of this article is 36 years old this year and has 8 years of experience in JAVA development. With three and a half years at Alibaba Cloud and four and a half years at Youzan, he is a typical middle-aged programmer. In this article, the author provides some practical advice regarding interviews and personal skills enhancement!
>
> **Content Overview**:
>
> 1. Personal introduction is an opportunity for a clearer, deeper, and comprehensive understanding of oneself.
> 1. A resume is a condensed essence of showcasing oneself and a chance to re-evaluate oneself and past experiences. It's not just about a brief introduction of skills and experiences, but also about highlighting one’s areas of advantage (differentiation) to the maximum extent.
> 1. Personally, I do not support mass job applications but prefer targeted applications. Finding the right direction to apply may result in fewer targets, but it is more efficient.
> 1. Technical exploration must start with understanding the principles. If you do not understand the principles, you will remain superficial and unable to truly grasp it. To what extent should one understand the technical principles? Data structure and algorithm design, considerations, technical mechanisms, optimization ideas. One must replay it in their mind until all details are clear. The ability to present it clearly and coherently is even better. Exploring technical principles must include reading source code. There is a difference between having read the source code and not having done so. If you haven't read the source code, you may articulate it, but there's still a layer of separation; having read the source code breaks through that layer, leads to personal understanding, and allows one to express it more confidently. Of course, it might be that I lack the ability to act.
> 1. Learn to be good at learning from failures. It was during a four-month learning, thinking, accumulating, and refining period in Hangzhou, along with reflections on interview failures, constant adjustments of strategies, improving preparations, and enhancing previous shortcomings that I managed to receive a satisfactory offer within just two weeks of returning to Wuhan.
> 1. An interview is a process of understanding both parties through communication. The questions during an interview can vary widely, but there are certain questions that need to be prepared in advance.
>
> **Original Article Link**: <https://www.cnblogs.com/lovesqcc/p/14354921.html>

Learn from every experience, and practice in everything. Be good at learning from setbacks.

## Prologue

I am 36 years old this year with 8 years of JAVA development experience. I spent three and a half years at Alibaba Cloud and four and a half years at Youzan, making me a typical middle-aged programmer.

Through many years of reading, learning, and thinking, my values, outlook on life, and worldview have gradually taken shape. I realized that my interest lies in education and culture, so impulsively, in late August, I quit my job to find new opportunities. Limited rationality could not restrain my impulsive nature. I do not recommend quitting without a plan; there should be a scientific and rational approach to work.

Although I initially believed that I “had ideals, goals, willingness, and capability,” finding a job in educational development should not be too difficult, but in reality, I was overly optimistic. The reality quickly showered me with cold water. I faced repeated defeats but continued to fight. I was surprised to find that I had this resilience. The interview process is a trial; if you are not defeated by failure, a resilience will grow from it, allowing one to go further. Who hasn’t gone through the trials of failure? Failure is the greatest teacher if you are willing to learn from it.

During the interview process, I quickly discovered my disadvantages:

- I invested energy into business, but my technical depth was insufficient, and my understanding of principles was limited to a shallow level;
- My perspective was not broad enough, limited to the order business line I worked on, and I did not understand other associated business lines (like products, marketing, payment, etc.) well;
- My thinking was not expansive; I spent most of my time on development and testing, with little thought on operations, products, business, or commercial aspects;
- I lacked management experience and was older; I once underestimated these two disadvantages, but they gradually became evident, leading to a loss of confidence, yet I ultimately pulled through.

But I also had my advantages. The fundamental law of professional competition is scarcity and differentiation. The ability to solve large project architecture designs and tackle technical challenges reflects scarcity; being able to do meticulous, thorough work, along with experience in high-concurrency, high-traffic system development reflects differentiation. Scarcity is the best strategy, differentiation is a middle-ground strategy, while compromising is the worst strategy.

I may lack the advantage of scarcity, but I do have some points of differentiation:

- I approach every job diligently, typically spending 3 to 5 years in each position, which gives me some prestige from large companies, leading to more interview opportunities (though it doesn't guarantee I will pass);
- I persist in writing blogs, tirelessly pursuing the "way" of software development, often reflecting on and recording problems and solutions encountered during development;
- I work seriously and rigorously, able to analyze and think about problems from a holistic perspective, while also paying attention to foundational improvements;
- I have practical experience in engineering quality, performance optimization, stability construction, and business configuration design;
- I have long-term development and maintenance experience in high-traffic microservice systems.

I didn’t send out many resumes. In the few interviews I had, I gradually realized that the claim of “landing offers from dozens of major companies” online is not credible. Here are the reasons:

- If one truly landed a large number of offers, the interview level is likely that of a junior engineer. To interview for a position as a senior engineer after over four years of experience involves extreme depth and breadth, encompassing everything from basic algorithms to various middleware principles and mechanisms, as well as practical operation architecture—it's an immersion in the “ocean of technology.” Unless a person's background and strength are very strong, and they have also done very deep and broad accumulation;
- A person with a strong background and strength wouldn't be interested in spending so much energy interviewing various companies just to boast about their capabilities; stronger individuals will have their own logic for choosing, and their resumes will be more targeted. Why wouldn’t they invest more energy in excellent companies that maximize their benefits?
- Advertisements by training institutions. They know very well that what new people need is confidence, even if it’s a fabricated confidence.

Alright, enough of the small talk. Let me talk about what I have undergone and reflected upon in interviews.

## Preparation

Life may be long, but the interview time is short, at most one hour or an hour and a half. How can others gain a clearer understanding of you, someone with over thirty years of life experience, in just one hour? This requires you to do a lot of detailed preparatory work. To some extent, interviews share similarities with dancing: five minutes on stage requires ten years of practice offstage.

The preparation work mainly includes resume preparation, personal introduction, understanding of the company, technical exploration, communication skills, common questions, mid-senior level positions, and maintaining a positive mindset. This preparation is a comprehensive and in-depth re-evaluation of oneself and the external world.

Initially, I thought I was well-prepared, simply tweaking my old resume. But with repeated setbacks, I discovered my preparation was insufficient. Now I believe that preparation is 70%, adaptability is 30%. Preparation means knowing oneself and the opponent, being aware of what questions will be asked (usually regarding system/project/technology depth and breadth), and understanding how to respond; adaptability involves reasonably demonstrating one’s problem-solving thoughts when faced with questions that are unknown or unclear, as well as identifying gaps based on unanswered questions during the interview and reinforcing the fundamentals.

This process is essentially a learning journey—continuous reflection and refinement, learning new content, and reevaluating oneself and past experiences.

### Resume Preparation

At first, I kept it rather simple. I took out my previous resume, added new work experiences, made slight modifications, yet the overall template remained largely unchanged.

On a basic level, I did it relatively carefully, honestly listing my familiar and proficient skills and experiences, and I tried my best to make it neat and visually appealing (having studied some UI design). Not overly extravagant or pretentious.

However, on the expansion level, I wasn't doing enough. One day, a headhunter called and asked me, “What is your greatest advantage?” I was momentarily at a loss for words. At that time, I didn't think much about it. After experiencing failures in subsequent interviews and feeling a bit insecure, I began to think carefully about my advantages. I then wrote “in-depth thought and practical experience in engineering quality, performance optimization, stability construction, and business configuration design” at the top of the "Skills and Qualifications" section, as this truly reflected what I had done, which was practical, grounded, and summarizable.

Sometimes, the order of the content in the resume is also very important. Previously, I listed the languages and technologies I mastered at the beginning, while placing “project management skills and team influence” at the end. However, after applying to Nian Gao Mama, I didn’t receive an interview and was triggered; I realized that the company might have considered my management experience insufficient. Therefore, I deliberately moved “project management skills and team influence” to the forefront, indicating that I valued management aspects. However, upon sending out the new resume, I received no response. I realized that such a rearrangement might mislead people into thinking I leaned towards management skills (in fact, one HR even asked me if I was still writing code). But in reality, I lacked in management, so eventually, I reverted to the original order, highlighting my “essence as an engineer.” Subsequently, I made further modifications to the phrasing.

As the interviews progressed, I sometimes found my resume lacking or that I hadn’t previously elaborated enough. For example, in the experience section regarding order exports, I only wrote about a significant improvement in performance and stability, making it seem qualitative. Therefore, I added some quantitative elements (2w blocks => 300w+, 1w/1min) for validation; for instance, after leaving my job in August and interviewing in December, there was a gap period, and some companies would inquire about it. Consequently, I added a statement explaining what I had been doing during this time; for example, representative systems and projects, knowing the value and significance of each system/project (though not necessarily needing to write it down, it’s important to have a clear understanding internally). One must put in the effort.

For instance, I detailed my work experiences and knowledge at Youzan, but the section for Alibaba Cloud remained largely untouched. However, some companies were more interested in that experience, while I felt there wasn’t much to say, and only a few memories remained vividly, along with some records of blog articles which seemed too scant compared to that work experience. Here, it wasn’t actually a resume issue, but a matter of retrospection regarding past experiences. It is advisable to write a self-review after each project ends, to avoid allowing time to dilute these valuable experiences.

Everyone actually has a lot to talk about, but how much is genuinely recorded? How much is worth discussing? Failing to put in effort in the past will lead to suffering in the interview.

**Insights on Updating the Resume**:

- A resume is a concentrated essence for showcasing oneself and an opportunity to re-evaluate oneself and past experiences;
- Not only should it briefly introduce skills and experiences, but it should also highlight one’s advantageous areas (differentiation) to the maximum extent;
- Enhance the description of work experience to emphasize contributions and gain recognition from others;
- Reflect and document the gains from every project to lay a solid foundation for job-hopping and interviews.

### Personal Introduction

Interviews usually require a brief personal introduction. The personal introduction often serves as a prelude and buffer stage for entering the interview, easing the tension.

Initially, my personal introductions covered aspects like personality, hobbies, work experience, aspirations, etc., but I seemed unsure of what to say. In reality, the personal introduction is a chance to fully showcase oneself. The introduction should clearly highlight one's core advantages (it requires excavating one’s experiences and refining them). My current personal introduction generally includes: personality (e.g., relatively quiet), working style (diligent and meticulous, with an emphasis on quality and holistic thinking), greatest advantage (owner mentality, execution capability, engineering control capability), and a brief summary of work experience (responsibilities at each company, contributions, and gains). The personal introduction should be concise and to the point.

The personal introduction is an opportunity for a clearer, deeper, and comprehensive understanding of oneself.

### Understanding the Company

Many people, like me, may know little about the company’s business yet directly submit applications. This is actually unreasonable. I personally do not support mass applications but prefer targeted ones. Finding the right direction is more efficient, even if it results in fewer targets. It’s like renting a house—I usually look for places on Douban. Although there are fewer sources, finding the right one is a stroke of luck.

Choosing a company to apply to should be because it aligns with your intentions and is worth pursuing, not just because it is a company. Just like seeking a partner, it’s not just about finding a woman. To determine whether a company meets your intentions, one should understand more about the company: its main business, future development and plans, its industry and position, financial status, and industry reputation and evaluations.

Bringing in discussions about the company’s business and thoughts during the interview can be a plus point. This can also be used in the “Do you have any questions?” area.

### Technical Exploration

Technical capability is a fundamental quality for a technical person. Therefore, I believe that regardless of the future job, strong technical competency is ultimately indispensable and cannot be overlooked.

Principles and design ideas are the most essential aspects of software technology. Generally, software technology can be divided into two aspects:

- Principles: the basic laws and processes of how things work;
- Architecture: the art of organizing large-scale logic.

**Technical exploration must start with understanding principles. If you do not understand the principles, you will remain superficial and cannot truly grasp it. To what extent should one master technical principles? Data structure and algorithm design, factors to consider, technical mechanisms, optimization ideas. One must replay them in their mind until all details are clear. Being able to articulate it clearly and coherently is even better.**

**Exploring technical principles includes examining source code. There is a distinct difference between having read the source code and not having done so. If you haven’t read the source code, you might be able to articulate, but there’s still a layer of separation; having read the source code breaks through that layer, allowing for personal understanding, and one can articulate with greater confidence. Of course, this may also reflect a lack of acting ability on my part.**

I personally do not strongly support question-centric interviews. While practicing questions can be a shortcut for entering the industry, it comes with disadvantages:

- It remains someone else's knowledge system rather than one's own summarized knowledge system;
- Technical exploration is to prepare for future work instead of responding to immediate needs; otherwise, even if you get in, you might still remain in a state of numbness.

Through systematic organization, I gradually formed a technical system structure suitable for myself: [“Common Technical Thoughts and Mechanisms in Internet Application Server”](https://www.cnblogs.com/lovesqcc/p/13633409.html). On this basis, reviewing interview questions for self-testing and addressing gaps is a more appropriate approach. I will delve deeply into this system.

### Communication Skills

Currently, the main form of interviews in most companies is conducted via oral communication, with only a small fraction having written or online tests. The limitations of oral communication are evident. The demand for communication skills is quite high while the emphasis on showcasing professional capabilities is not as prominent. It is challenging to present the depth and breadth of a person’s expertise and experiences through a few minutes of expression. Often, the greater the depth and breadth, the more difficult it is to articulate. Technical staff often neglect expression.

I usually write more and speak less, and when I speak, I am not fluent. Sometimes I would dive in without clarifying the background and instead become verbose, jumping around in my exposition (this approach might be more suitable for writing novels), which leaves interviewers confused at times. The coherence and clarity of expression are crucial. You might as well test yourself: What is the architectural design of Dubbo? What is the persistence mechanism of Redis? Then try answering it.

Basic rules for communication skills:

- Start with the main point and then detail; first the overall view, then the specifics;
- Present the basic idea before discussing optimizations;
- Reflect interaction. Begin with an overview, then ask the interviewer what they'd like to hear about, followed by detailed exposition. Avoid dumping all your thoughts at once, catching the interviewer off guard; for scenario-based system design questions, ask clarifying requirements, such as time constraints, space requirements, data volume or concurrency considerations, and whether to consider specific situations.

### Common Questions

The interview is a process of understanding both parties through communication. The questions during interviews can vary widely, but there are several questions that need to be prepared in advance.

For example, the “soul questions”:

- Why did you leave XXX?
- What is your expected salary?
- You have a gap period; can you explain what happened?
- What are your career plans?

High-frequency technical questions:

- Basics: Data structures and algorithms, networks;
- Microservices: Technical systems, components, infrastructure, etc.;
- Dubbo: Overall architecture, extension mechanisms, service exposure, referencing, calling, graceful shutdown, etc.;
- MySQL: Indexing and transaction implementation principles, SQL optimization, partitioning;
- Redis: Data structures, caching, distributed locks, persistence mechanisms, replication mechanisms;
- Distributed: Distributed transactions, consistency issues;
- Message middleware: Principles, comparisons;
- Architecture: Architectural design methodologies, architecture experience, design patterns;
- Performance optimization: JVM, GC, application-level performance optimization;
- Concurrency basics: ConcurrentHashMap, AQS, CAS, thread pools, etc.;
- High concurrency: IO multiplexing; cache issues and solutions;
- Stability: Thoughts and experiences on stability;
- Production issues: Tools and troubleshooting methods.

### Mid-Senior Level Positions

It might seem that I am not particularly confident. I apply with the mindset of “being a solid engineer.”

For middle-aged programmers, companies have higher expectations. Each of my applications for “Senior Engineer” positions automatically translates to “Technical Expert” or “Architect.” I feel the pressure with no way to refute it. Interviews for mid-senior level positions require more preparation:

- Do you have experience leading a team?
- How much of your X years of working experience has been spent on architecture design?
- What does the architecture process look like? What architectural design ideas or methodologies do you have?

If you are unprepared and suddenly thrown such questions, it would be easy to get flustered. In reality, I may have harbored a bit of luck, treating “Technical Expert” and “Architect” positions as “Senior Engineer” interviews, which led to a series of failures. Clearly, I reversed the order: I should be interviewing for senior engineer positions with the parameters of a “Technical Expert” or “Architect.”

Well then, let’s embrace the challenge! I am not one to shy away from challenges.

Additionally, when preparing for “Technical Expert” and “Architect” roles, one should allocate at least a day for preparation. Those already possessing rich experiences may skip this.

### Good Mindset

Maintaining a good state of mind is also particularly important. I have gone through a process of mental changes: “optimism - insecurity - regaining confidence.”

For a long time, due to “desire for results,” I was cautious and somewhat nervous, fearing that I would fail to answer a technical question. As a result, I often struggled to articulate clearly or present structured thoughts. Approaching interviews with the mindset of “securing an offer” was indeed uncomfortable, making each interview feel quite passive and leading me to consider lowering my standards.

At times, I wondered: How did I end up like this? By rights, at this time, I should have been capable of pursuing my beloved career! Perhaps I had been a bit lax previously, with a narrow vision and insufficient accumulation, leading to my current unfavorable situation.

I am a punctual person and hope that others will be as well. Most interviewers in Hangzhou were punctual, and even if someone was late, it was within an acceptable psychological range. However, after returning to Wuhan for interviews, the pace seemed to be slightly thrown off by a few companies. There were one or two instances where I wasn't even sure when the interviewer would join the meeting. I wondered, is this the sort of “hospitality” talent should expect? I felt slightly offended. Nevertheless, I politely expressed that it was fine. Yet, I firmly believe: an interviewer being late is a disrespect to talent. I have reservations about entering companies that do not respect talent. A good bird chooses its tree to rest on, a good servant chooses their master to serve. Can I abandon some basic principles and bottom lines and succumb to an offer that disrespects talent simply because of my current unfavorable circumstance?

I realized: One should earn the respect and appreciation of others through their abilities so that future collaborations can proceed more smoothly. Rather than forcing someone to stay, it is better to let them go if things are not meant to be. Regardless of others’ doubts, one should focus on honing their capabilities, uncovering their talents and advantages, and they will ultimately shine. Therefore, my mindset shifted dramatically: I should focus on communicating, fully understanding and knowing the other party, to earn their genuine acknowledgment instead of merely obtaining a ticket to entry, becoming a tool for work.

There is a little story about “stones and jade”: Treat yourself as a talent, and strive to improve yourself to gain the “treatment of talents”; Treat yourself as a stone and sell yourself cheap, relax in your efforts, and you will only receive the “treatment of stones.” Although one might not immediately possess the abilities of a talent, in one’s heart, one should start observing potential employers from the perspective of a talent, rather than solely looking for a job that pays “more money.”

Moreover, anxiety is unnecessary. Anxiety essentially arises from the gap between reality and goals. One can always assess the rationality of their goals and how to achieve them. If the goals are too high, then appropriately adjust the goal level; if the goals are achievable, then make reasonable decisions and realize the goals through continuous effort and well-timed actions. Decision-making, effort, and action capabilities can all be continually cultivated.

## Interview Experiences

Technical interviews focus more on technical depth and breadth, so thorough preparation is still essential. The circumstances for interviewers and candidates differ; an interviewer only poses a few points, but collectively, many interviewers create a full picture. Once you understand this, do not become complacent and think you are superior to the candidates.

I did not go through many companies for interviews, as I had already decided to pursue a career in education, directly filtering out numerous interview invitations based on my “interest and motivation.” Most of the interviews I had in Hangzhou were with educational companies, and I even declined offers from companies like Alibaba and Huawei (though I might not have made it).

While my approach may seem “straightforward,” it is indeed worthwhile to invest most effort in the industry and career I wish to pursue.

What I consider education does not only include the commonly discussed online education or K12 education, but rather an educational system encompassing any effort that can better achieve educational outcomes, including but not limited to teaching, reading, music, and design.

### Relays Technology - Senior Engineer

This was the first company I interviewed with. After an enjoyable discussion, I heard nothing further. However, I didn't pay too much attention to it. The interviewer asked mostly about transaction-related business aspects, with the deeper question being how to ensure data consistency across applications.

At this point, I feel like I threw down a small stone to explore the path ahead, still unaware of my circumstances.

### NetEase Cloud Music - Senior Engineer

Next was NetEase Cloud Music. A large company is indeed a large company. The first interview was filled with questions on caching, distributed locks, Dubbo, ZK, and related mechanisms. Unfortunately, due to my limited depth of understanding of technical principles, I essentially hung up spectacularly with a “I only know bits and pieces” kind of performance.

At this moment, I became acutely aware of the shortcomings in my technical foundation, prompting me to embark on broad technical learning and deepening my understanding of principles and logic, systematically organizing and summarizing my knowledge, ultimately forming my own internet server technology knowledge system.

### Ming Shitang - Technical Expert

This interview was for an architect role. They asked a relatively broader range of questions, covering DB, Redis, etc. The feedback was that my technical skills were decent but I lacked management experience. This was the first time I realized that middle-aged programmers lacking management experience could be disadvantageous. When applying for technical expert roles in small and medium enterprises, the requirements often include management experience. This is something to be aware of during applications.

What should I do if I lack management experience? After some reflection, my thoughts are:

- Change what can be changed, and learn from what cannot be changed. For instance, studying technical principles is something I can change, while management experience is harder to change instantly; thus, I should learn more about fundamental management theories.
- Uncover relevant experiences from my history. Although I may not have formal experience leading a team, I do have experience managing projects and engineers, as well as basic management experience over a business line. I should unearth these experiences more.

### Byte Education - Senior Engineer

During the Byte Education interview, I dug quite a few holes for myself.

For instance, when the interviewer asked me to talk about a project that I felt proud of, I chose a project from nearly four years ago: the cyclical purchasing project. Although this was my first representative project upon joining Youzan, the time lapse was too long, and I had not documented it thoroughly, forgetting many technical details. I highlighted the “integrated” design concept that left a lasting impression on me, but I failed to recall why this concept emerged (not having documented it carefully).

Furthermore, during a scenario-based question regarding a class, I asked whether to use a CS or BS architecture. The interviewer said CS. Wasn’t that digging a hole for myself? Clearly, I wasn’t familiar with CS architecture; why not ask about BS architecture from the start? Sigh!

The feedback from Byte Education stated I had good business sense but needed to improve my system design skills. I found it quite candid. Thus, I began to place more focus on reading articles and practicing thoughts on real-world system design.

Lessons learned:

- When working on projects, diligently document the technology stack, design decisions and their rationale, and technical details to lay a foundation for interviews;
- Prepare prominent and representative systems and projects in advance to avoid choosing ones that are too distant in time without detailed records;
- Select familiar projects and architectures to create a good first impression; otherwise, the interviewer will think you don't possess relevant knowledge.

### Migu Digital Media - Architect

This interview involved three interviewers simultaneously. Perhaps I had experienced too few interviews before. It seems that state-owned enterprises appreciate such formats for higher-level positions. Inquiry fosters insight while partiality leads to ignorance. The questions were quite broad, ranging from the basic principles of ES to data migration in server rooms. Some technical mechanisms, while I had studied them, were unclear in my mind, leading to poor responses. For example, when discussing ES's search principles optimizations, after mentioning inverted indices, I was unable to articulate Term Index and Trie trees. This indicated that knowing something does not equate to truly understanding it. Genuine understanding requires the ability to articulate thoughts and details clearly.

What left a deep impression was one particular question: What architectural thoughts do you hold? This was my first experience being asked about architectural design concepts, and I suddenly felt a bit flustered. Although I frequently contemplate such issues and have written articles on them, I had not formed a systematic, refined methodology, resulting in a somewhat disorganized response.

### Tuya Smart - Senior Engineer

I applied to Tuya Smart because I found this company to be promising. An excellent company deserves at least a conversation; who knows? There might be opportunities for collaboration in the future! One's perspective on issues should be broad; one should not cling solely to their own ideas.

Overall, my experience with Tuya Smart was positive. The interviewers were courteous and patient, covering the overall architecture, technologies, and projects extensively with questions that fell into my realm of expertise, to which I responded fairly well. Perhaps my experiences matched their needs perfectly.

If it weren’t for my particular strong inclination towards education, it’s quite likely I would have joined Tuya Smart. In my view, the Internet of Things is an intriguing field.

### GoGo Learning - Technical Expert

I was able to answer questions at GoGo Learning for the most part. However, the feedback pointed out that I lacked focus on what was being asked and that my technical summarization was insufficient. At that time, I felt a bit indignant, considering how many articles I had written—I thought I had done quite a lot of thinking; how could it be considered insufficient? It was merely a matter of technical blind spots. The sea of technology is vast; who can lack blind spots completely?

However, upon reflection, I recognize that I indeed fell short of the requisite level of understanding. My summaries regarding technical principles and mechanisms, as well as troubleshooting in production situations, lack clarity and detail. This is something I need to work on continuously.

Moreover, through more interviews, I noticed an evident deficiency in my communication skills. I tend to be verbose, tend to elaborate indefinitely on a minor point, often lose context while diving straight into solutions, and retreat into a pattern of circular reasoning, which can cause interviewers to lose patience. I should adhere to core logic like “start with the main point, then break it down,” as well as “basic idea—implementation—optimization” when responding. Communication skills are critical and cannot be neglected just because one focuses on coding. Each time I interview with an educational institution, I inevitably feel nervous, fearing I might miss the opportunity.

This was the second company to directly point out that my age and experience did not match, heightening my concerns about being over-aged and causing me to lose a bit of confidence.

So, how did I regain confidence? There’s an old saying: “As long as the green hills remain, one need not worry about firewood.” Even if I am older, if I can polish my technical abilities to a high degree, I refuse to believe that I won’t find a company that recognizes my worth. At worst, I could work on open-source projects. Having strong technical abilities does not necessitate functioning exclusively within a corporate framework; neither should I confine myself to the views of those blinded by age prejudice. External validation is important, but internal value far outweighs anything superficial.

### Yi Tong Wenchuang - Architect

This interview also involved three interviewers, mainly focusing on project experiences while the technical questions were not too deep. I felt I answered reasonably well. The interviewers also asked questions related to architectural design, and my responses were average. At that moment, I still had not realized that I was interviewing for an “Architect” position while presenting myself at the level of “Senior Engineer.”

The interviewers were quite mild, and HR was actively in touch and communicating; the atmosphere felt positive. However, I did not proactively inquire about the feedback, so I did not hear back after that.

### New Oriental - Senior Engineer

I interviewed with New Oriental mainly because it aligned with my ambition in education, even though the position was for an information management system, which differed somewhat from my ideal business focus. After discussing further, it turned out they were seeking engineers more familiar with operations, a domain in which I lack familiarity and attention. Thus, I didn’t quite meet their genuine recruitment expectations. The interviewer, who was warm, was originally from Yichang—my university town; the interview experience was great.

In the future, I need to dedicate time to learn about operational aspects. As an excellent engineer and a qualified architect, one should broadly learn and become familiar with various components, middleware, and operational deployments used in systems. One should possess an overarching view, although I might have realized this a bit late. Better late than never.

### ZOOM - Senior Engineer

The interview with ZOOM left a rather sour impression. There were two interviewers present; one seemed very patient, while the other, portly and yawning, appeared somewhat uninterested in both the interview and the candidate. I thought to myself, if you don't feel like interviewing, why come at all? Do you think candidates are beneath you? I could easily dash you a smart retort. Nevertheless, I remained polite, acting as though nothing happened. The company is selecting talent, and candidates are also selecting companies.

Reflecting back, ZOOM was the remote communication tool our company used during the pandemic, which left a good impression; discovering such engineers and interviewers within was quite surprising. Is he really that legendary? As far as I know, foreign technology in the realm of internet software basically crushes domestic options. Most Chinese companies use or customize foreign frameworks, middleware, and infrastructures; what boast is there to be had?

### Ayou Culture - Senior Engineer

Ayou Culture had four rounds of technical interviews. The first technical interview left a strong impression on me. The interviewer seemed well-versed in the principles and mechanisms of operating systems, and I struggled to answer many questions. I initially thought I might fail the interview, but surprisingly, I was given another chance to prove myself. The second interviewer covered project experiences and technical questions, which I was quite familiar with. The third interviewer asked a range of questions, some of which I answered well, and some where I struggled. Fortunately, they were very patient.

Overall, the atmosphere during the interview was quite relaxed. However, at that time, Ayou’s hiring needs were not urgent; they likely hoped to reach out again when opportunities arose. Unfortunately, I was preparing to return to Wuhan at that time, mainly considering my elderly parents, and I wished to spend more time with them.

Considering this, I realized my approach to problem-solving and decision-making was rather simplistic, lacking the ability to make more intricate calculations and balances.

### Xiaomi - Expert/Architect

I applied to Xiaomi mainly because the position was very similar to my previous role at Youzan, both involving transaction platforms. After browsing their website, I found their work impressive, yet it didn’t align closely with my aspiration in educational culture.

I lacked a strong desire to join Xiaomi, resulting in my dwindling motivation for the interview.

### Visual China - Senior Engineer

They asked questions centered around technology, projects, and experiences. Overall, the depth of technical questions wasn’t too difficult, and they did involve project elements. The HR representative was pleasant, and instead of “bombarding” me regarding my experiences, I explained Youzan's product services and business model while slightly introducing my background.

### iFlytek - Architect

In the first two interviews, I felt the interviewers were disinterested in the arrangements made for the interviews. An architect's role is one that demands very high technical and design capabilities. In the first interview, they asked several questions on technology and architecture; however, in the second round, most questions revolved around my background and non-technical aspects, seemingly focusing more on my outward attributes rather than my technical and design capabilities. The discussions felt superficial.

While abilities can differ in level, the fundamental etiquette of respecting talent remains unchanged. Respecting talent implies focusing on their abilities and skills, not on aspects unrelated to talent.

### Qingteng Cloud - Senior Engineer

The technical interview process at Qingteng Cloud was gentle. There was a sense of open and candid communication, and I felt valued. Their enthusiasm for hiring talent coincided with my previous belief in “earning respect and appreciation through abilities.”

### Tencent Conference - Senior Engineer

I interviewed for a position at Tencent using Tencent's own meeting software. Due to unstable internet connections, the interview was riddled with interruptions, and I struggled to get through sentences clearly. Nevertheless, we remained patient and ultimately completed the interview. Interviews should be seen as a confrontation of wisdom and strength between both parties, and more importantly, as a cooperative endeavor to achieve something while discovering mutual collaboration potential. Hence, the traditional view of interviews as a singular testing mechanism requires reevaluation.

Since I had already received an offer and this particular role did not align closely with my original intentions, I communicated with Tencent and decided to withdraw from further interviews.

### Final Choices

When receiving multiple offers, how do I choose? Personally, I focus on:

1. Interests and motivations;
1. Salary and benefits;
1. Company development prospects and personal growth opportunities;
1. Work environment;
1. Small but capable enterprises.

How do I select between Visual China and Qingteng Cloud? Here's a comparison:

- Salary and benefits: Both offer competitive packages and recognize my potential; Visual China’s offer is for a Lead position, while Qingteng Cloud promises roles in core business;
- Work environment: Qingteng Cloud should lean towards an engineering culture, while Visual China focuses more on business;
- Challenge: Qingteng Cloud presents a more robust technical challenge, while Visual China poses a stronger business challenge;
- Interests and motivations: Visual China aligns better with my desire to engage in cultural endeavors, while Qingteng Cloud does not closely fit my educational aspirations and leans more towards fundamental technical work (while I prefer more humanistic endeavors). However, Qingteng Cloud’s focus on safety is of great value and meaningful. Furthermore, safety in the future can serve the education sector, providing a sort of circumstantial rescue. Particularly, founder Zhang Fu’s idealistic belief “to let the light of security illuminate every corner of the internet” and his active pursuit inspire me. In the end, I feel that opting for safety slightly outweighs the notion of engaging in image copyright protection.

Additionally, I think that education, as it suits me best, should focus on coding education or engineer education. I want to become a systems designer, requiring more practical production experience. I may interact more with junior to mid-level engineers, providing training and guidance within the company. Alternatively, during my spare time, I may record videos for Bilibili to serve a broader audience. In the future, I might even write a book on coding design, consolidating what I have learned throughout my life.

Thus, after careful consideration over a day, I decided to join Qingteng Cloud Security. Of course, making this choice also means embracing a greater challenge: I am starting from nearly zero in security knowledge and must learn a vast array of knowledge and experience, which constitutes a significant challenge for me as a middle-aged programmer.

## Summary

Many issues have solutions, and the plight of a “middle-aged programmer” seeking a job is no exception. By establishing clear and defined goals, making scientifically sound decisions, sustaining efforts, mastering the basics, and striking appropriately, one can ultimately reap the rewards of victory. However, it's crucial to emphasize: The effort must be made during normal times. If one neglects accumulation during regular moments, it leads to needing to spend even more time studying during interviews, facing setbacks, and experiencing a bumpy path, making it quite uncomfortable. It's always better to distribute effort over time. Additionally, maintaining an open perspective is essential—do not wait until interviews to have an epiphany.

An important lesson is to learn from failures. The continuous learning, thinking, accumulating, and refining periods during a four-month gap in Hangzhou, alongside reflections on interview failures, constantly adjusting strategies, perfecting preparations, and enhancing previous shortcomings allowed me to receive a satisfactory offer within just two weeks upon returning to Wuhan.

Moreover, it’s worth mentioning that for technical personnel, writing blogs is an incredibly valuable endeavor. Interviews are limited by the communication format and cannot filter out candidates who may be suited for the role:

1. The interview time is short—even experienced interviewers can overlook potential (a fundamental limitation);
1. The interviewer may ask something for which the candidate is unprepared (an issue of luck);
1. The interviewer could be in a bad mood and uninterested (another issue of luck);
1. The interviewer’s own level of understanding.

Thus, possessing true skills yet being passed over should not lead to heartbreak. The value of writing blogs lies in providing additional dimensions through which to exhibit one’s thinking and day-to-day work.

Quality organizations looking to respect talent will seek to understand candidates from multiple perspectives (choosing between strengths and weaknesses to confirm fit), including blogs; while companies that do not respect talents will lean towards lazy methods that disregard the genuine abilities of candidates, using superficial standards for quick filtering—efficient, yes, but ultimately it doesn’t enhance their talent recognition capabilities.

Having undergone this series of interview trials, I feel I have made quite a bit of progress compared to the person I was when I left. While not a complete transformation, at the very least, I have shed a layer of skin. There are still differences, particularly in interviewing for technical expert and architect positions in well-known large companies. This gap relates to the challenges faced in my daily work, the limitations of my cognitive horizons, and insufficient summarization. Next time, I hope to accumulate enough strength to perform even better, drawing closer to valuable endeavors that resonate with my heart.

Interviews can also be regarded as an experience in one’s career.
