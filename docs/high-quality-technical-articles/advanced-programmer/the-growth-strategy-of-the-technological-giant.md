---
title: Programmer's Technical Growth Strategy
category: Selected Technical Articles
author: Bobo Weike
tag:
  - Leveling Strategy
---

> **Recommendation**: An excellent article by Teacher Bobo, which is not only helpful for technical growth but also applicable in other fields! It is recommended to read it repeatedly to form your own technical growth strategy.
>
> **Original Article URL:** <https://mp.weixin.qq.com/s/YrN8T67s801-MRo01lCHXA>

## 1. Introduction

In Bobo's WeChat technical exchange group, students often ask questions about how technical individuals can learn and grow. Although this is a WeChat exchange, I can still sense the anxiety among my peers.

**Why are technical professionals anxious?** To put it bluntly, it is a lack of courage and a narrow perspective. "Courage" refers to bravery; anxious individuals generally fear the uncertainty of the future. "Perspective" refers to insight; anxious individuals typically fail to see the surrounding world clearly and don't understand themselves and the path suitable for them. "Perspective" can also refer to ambition; those who are easily anxious often have narrow vision and small ambitions. From a strategic and managerial perspective, this reflects insufficient awareness of oneself and the surrounding world, a lack of a clear and long-term learning and growth strategy, and no executable phased goal plans combined with strict execution.

Since there are many students asking such questions, it has become somewhat tedious for me. To avoid repeated responses, I have specially summarized this long article, attempting to address these questions uniformly. If any students ask similar questions in the future, I will guide them to read this article and then encourage them to take three months, a year, or even longer to think through and answer a question: **What is your technical growth strategy?** If you can clearly answer this question, you can simply follow through with execution, and there is no need for anxiety; achieving your strategic goals and accomplishments is just a matter of time. Otherwise, you still need to refine and reflect continuously, making sure to understand this significant life question!!!

Now, let’s look at how some industry technical experts approach their growth strategies.

## 2. Learn Growth Strategies from Technical Experts

We know that software design has design patterns; in fact, there are also growth patterns (Growth Patterns) for technical professionals. Bobo often reviews the growth histories of technical elites on LinkedIn to explore their growth patterns, inspiring the formulation of his own technical growth strategy.

Of course, very few technical elites will explicitly tell you about their growth strategies and the segmented implementation plans for each year. However, this does not prevent us from tracing their growth strategies through their past experiences and results. In reality, **the more exceptional the technical person, the clearer their growth strategy and path become, making it easier for us to identify successful patterns.**

### 2.1 Case Study of a Systems Performance Expert

Most domestic developers are enthusiastic about system performance optimization, with some individuals constantly discussing high performance and high concurrency, yet only a few truly penetrate this field and reach an expert level.

The technical elite I want to introduce here is **Brendan Gregg**, the author of the classic book in the systems performance domain, **System Performance: Enterprise and the Cloud** (Chinese version: [性能之巅：洞悉系统、企业和云计算](https://www.amazon.cn/dp/B08GC261P9)), and also the creator of the famous [Flame Graph](https://github.com/brendangregg/FlameGraph) performance analysis tool.

Brendan Gregg was previously a senior performance architect at Netflix, working there for nearly seven years. In April 2022, he left Netflix to join Intel in an academic position.

![](https://oss.javaguide.cn/github/javaguide/high-quality-technical-articles/cdb11ce2f1c3a69fd19e922a7f5f59bf.png)

Overall, he has been deeply immersed in the realm of system performance for over a decade. You can view [Brendan Gregg's past experiences](https://www.linkedin.com/in/brendangregg/) on LinkedIn. During these ten years, Brendan Gregg has produced more than a hundred technical documents, presentations/videos, and various tool software related to system performance, all neatly shared on [his technical blog](http://www.brendangregg.com/). He can be considered a highly productive technical expert.

![Performance Tools](https://oss.javaguide.cn/github/javaguide/high-quality-technical-articles/format,png-20230309231802218.png)

The above image comes from Brendan Gregg's new book, **BPF Performance Tools: Linux System and Application Observability**. From this image, it's evident that Brendan Gregg's mastery of the system performance field has delved into every corner of hardware, operating systems, and applications. One could say he has a 360-degree unobstructed view; the entire computer system is almost transparent to him. Bobo believes Brendan Gregg is undoubtedly a world-class figure in the field of systems performance.

### 2.2 From Open Source to Enterprise Case Study

The second technical elite I want to share is **Jay Kreps**, the founder/architect of the well-known open-source message broker Kafka, as well as the co-founder and CEO of Confluent, a technology company that develops enterprise-level products and services around Kafka.

From [Jay Kreps' LinkedIn profile](https://www.linkedin.com/in/jaykreps/), we can see that he worked at LinkedIn for over seven years (June 2007 - September 2014), rising from senior engineer to engineering manager, ultimately serving as a principal engineer. Kafka was a project initiated by Jay Kreps at LinkedIn around 2010 to solve internal issues with data collection, storage, and consumption. He and his team then focused on refining Kafka, open-sourcing it (early 2011), and building its community ecosystem.

By the end of 2014, Kafka had become very successful in the community, with a significant user base. Consequently, Jay Kreps, along with several early authors, left LinkedIn to establish [Confluent](https://tech.163.com/14/1107/18/AAFG92LD00094ODU.html) and began the journey of providing enterprise-level services for Kafka and related products. As of April 2020, Confluent had secured $250 million in Series E funding, with a company valuation reaching $4.5 billion. From the inception of Kafka until now, Jay Kreps has invested almost a full decade into this product and company.

![Confluent Founders Trio](https://oss.javaguide.cn/github/javaguide/high-quality-technical-articles/format,png-20230309231805796.png)

The above image shows the three founders of Confluent, a very interesting combination, with one Chinese individual (left), one Indian individual (right), and Jay Kreps as the American in the middle.

The reason I have a particularly deep impression of Kafka and Jay Kreps is that in the second half of 2012, I was also engaged in big data collection at Ctrip's architecture department, where I developed a product similar to Kafka called Log Collector + Agent. I recall during that period, there were more than four similar open-source projects: Facebook Scribe, Apache Chukwa, Apache Flume, and Apache Kafka. Looking back now, only Kafka has thrived and developed successfully, which is inseparable from the dedication and sustained input from its founder. Of course, the high-level technical perspective of several founders also plays a crucial role.

At that time, I had almost no concept of strategic thinking and was at the stage of **wanting to learn everything, believing that doing more projects would make me a better engineer**. After half a year of working on data collection, I shifted to other "more interesting" projects (this also reflects the limited technical perspective I had back then). Throughout this time, I paid attention to Jay's entrepreneurial movements, yet I was surprised by the scale Confluent has reached today. In retrospect, ten years ago, Jay Kreps had fairly clear strategic thinking regarding his technical growth, coupled with significant technical perspective and necessary traits for success. Jay Kreps and Kafka provided me with a vivid lesson in technical strategy and practice.

### 2.3 Technical Media Influencer Case Study

At this point, some students might rebut, “Bobo, the elites you're mentioning all have great academic backgrounds and solid foundations, which is why they can achieve greater success.” This is not entirely the case; I want to introduce another technical media influencer, **Brad Traversy**, whose [LinkedIn resume](https://www.linkedin.com/in/bradtraversy/) reveals an average background, having attended a non-formal community college (equivalent to a diploma), and lacking formal work experience in major companies, with limited job roles mostly in website outsourcing.

![](https://oss.javaguide.cn/github/javaguide/high-quality-technical-articles/30d6d67dc6dd5f9251f2f01af4de53fc.png)

But!!! Brad Traversy is currently a big name in the technical media field; his [YouTube channel](https://www.youtube.com/c/TraversyMedia) has over 1.38 million subscribers, and over ten years he has produced more than 800 teaching videos on web development and programming. Brad Traversy is also a successful instructor on [Udemy](https://www.udemy.com/user/brad-traversy/), having released 19 courses with close to 420,000 students enrolled.

![](https://oss.javaguide.cn/github/javaguide/high-quality-technical-articles/160b0bc4f689413757b9b5e2448f940b.png)

Currently, Brad Traversy works as a freelancer, and his income from YouTube advertisements and Udemy courses is quite substantial.

It is hard to imagine that this major figure in technical media once bore labels of a troubled youth—drinking, smoking, using drugs, getting tattoos, and even going to jail... It wasn't until the birth of his first child after marriage that he began to take responsibility and change his ways. Driven by his passion for technology, he started to consistently provide free courses on YouTube. From there, he found a strategic goal that suited him, leading to various positive changes in his life. If you're interested in Brad Traversy's past experiences, I recommend watching his self-telling video on YouTube, [My Struggles & Success](https://www.youtube.com/watch?v=zA9krklwADI).

![My Struggles & Success](https://oss.javaguide.cn/github/javaguide/high-quality-technical-articles/format,png-20230309231830686.png)

I briefly browsed through [Brad Traversy's entire collection of videos on YouTube](https://www.youtube.com/c/TraversyMedia/videos). Over the span of ten years, he has produced more than 800 videos, averaging over 80 per year. His first video was submitted in August 2010, and in the early years, he hardly gained subscribers; it was not until January 2017 that his subscriber count reached 50k, almost six years in. In October 2017, the subscriber count surged to 200k, and by March 2018, it reached 300k. As of January 2021, his subscriber count has reached 1.38 million. From 2017 onward, we can consider that after six to seven years of accumulation, his subscriber growth began to show an inflection point. **If we were to visualize this data, it would form a beautiful compounding growth curve.**

### 2.4 Case Summary

Brendan Gregg, Jay Kreps, and Brad Traversy each took different technical paths, yet their successes share commonalities or patterns:

**1. They have found their long-term strategic goals that suit them.**

- Brendan Gregg: Become a top expert in the field of system performance.
- Jay Kreps: Establish an enterprise service company based on the open-source message queue Kafka and take the company public.
- Brad Traversy: Become a major player and course instructor in the technical media field, making this his profession.

**2. They focused on and delved deeply into one (or a few related) niche areas, maintaining their resolve without casually switching fields.**

- Brendan Gregg: Systems performance field.
- Jay Kreps: Message middleware/real-time computing field + entrepreneurship.
- Brad Traversy: Technical media/teaching field, focusing on web development + programming languages.

**3. Long-term investment; each has consistently invested for 10 years.**

**4. Annual segmented plans + continuous measurable value output.**

- Brendan Gregg: Besides his regular company work output, he produces over ten technical documents and presentation videos each year, averaging 2.5 open-source tools released annually. Over ten years, he has published two books, with **System Performance** now in its second edition.
- Jay Kreps: Overall outputs include open-source products and company deliverables, plus one published book and numerous releases of Kafka and related products every year.
- Brad Traversy: He releases free YouTube videos each year (averaging over 80) and offers courses on Udemy (averaging 1.5 courses annually).

**5. Starting with the end in mind is a significant difference between elite individuals and ordinary ones.**

Ordinary individuals usually think step by step, rarely planning for the long term. Elite individuals often start with grand goals, employing a reverse-engineering approach to refine those goals into detailed implementation plans for each year/month/week. Brendan Gregg, Jay Kreps, and Brad Traversy exemplify the practice of starting with the end in mind.

![Start with the end in mind](https://oss.javaguide.cn/github/javaguide/high-quality-technical-articles/format,png-20230309231833871.png)

The growth models of these technical elites summarized above highlight a key point: their growth was driven by **persistent valuable output**. Understanding why sustained output is crucial leads us to the following concept of the learning pyramid.

## 3. Learning Pyramid and Deliberate Practice

![Learning Pyramid](https://oss.javaguide.cn/github/javaguide/high-quality-technical-articles/format,png-20230309231836811.png)

The learning pyramid is the result of research from the National Training Laboratory in Maine, USA, which suggests that:

> 1. After attending lectures, the average retention rate of the content learned is approximately 5%.
> 1. The average retention rate from reading books is about 10%.
> 1. Courses coupled with audiovisual effects yield an average retention rate of around 20%.
> 1. When a teacher conducts hands-on experiments/demonstrations, the average retention rate rises to about 30%.
> 1. Group discussions (especially after debates) can achieve an average retention rate of 50%.
> 1. Applying what is learned in practice leads to an average retention rate of about 75%.
> 1. Analyzing and teaching others based on what has been learned can yield an average retention rate of 90%.

The first four learning methods are termed **passive learning**, while the last three are considered **active learning**.

To draw an analogy with swimming, passive learning is akin to watching others swim, whereas active learning involves getting in the water and swimming yourself. We know that activities like swimming or running burn calories in the body, achieving physical fitness and muscle growth (muscle is the result of calorie burning). If you only watch others swim without ever trying it, you won’t build muscle. Likewise, active learning also requires "burning calories" in the brain to achieve effective training of the brain and expand its "muscle."

It's understood that burning body calories typically induces discomfort; if burning calories felt comfortable, there would probably not be overweight individuals in the world. Similarly, burning calories in the brain can lead to discomfort, tension, sweating, or incoherence; if burning brain calories were comfortable, it’s likely everyone would be smart and fully realize their potential. Of course, this discomfort is temporary; in the long run, it makes you healthier and smarter. Bobo has always believed that **the innate physical condition among individuals is relatively similar, but the differences in postnatal physical quality and capabilities primarily stem from the quality, frequency, and intensity of subsequent training for both body and brain.**

Once you understand this principle, mature-minded and self-disciplined individuals will engage in ongoing **deliberate practice**. This deliberate practice includes physical training; for instance, Bobo currently runs 3km and walks 3km daily, does 60 sit-ups daily, and holds a plank for five minutes each day, maintaining a certain level of calorie burning for the body. Deliberate practice also encompasses mental training; for instance, Bobo currently writes code (training the brain and hands) daily, produces ten minutes of free video content on Bilibili each day (training the brain and verbal expression), regularly sums up and publishes articles on his public account (training the brain and written expression), and plays balance ball games (as shown below) or Tomb Raider games for about half an hour daily (training the cerebellum and hands). Every day, he keeps his brain burning a specific amount of calories while maintaining a certain intensity (a moderate level of discomfort).

![Balance Ball Game](https://oss.javaguide.cn/github/javaguide/high-quality-technical-articles/format,png-20230309231839985.png)

For specialized principles and methodologies regarding deliberate practice, I recommend reading the book **Deliberate Practice**.

![Deliberate Practice](https://oss.javaguide.cn/github/javaguide/high-quality-technical-articles/format,png-20230309231842735.png)

It’s important to note that if you have never engaged in weightlifting, suddenly doing weightlifting one day will be quite uncomfortable, or even lead to injury. The same goes for brain training; if you have never produced video content, it will feel very awkward when you first start, and the quality of initial videos may not be great. Nevertheless, any form of training is a progressive and strengthening process. Once the "muscles" in the brain develop, you'll gradually enter a positive feedback loop, and it will get easier, leading to the development of related "muscles." Therefore, like physical fitness, mental training must not be given up due to difficulties; instead, it should be approached gradually (Incremental) and continuously (Persistent) through deliberate practice.

After understanding the learning pyramid and the concept of deliberate practice, we can now observe how elite figures like Brendan Gregg, Jay Kreps, and Brad Traversy operate their learning and growth, all based on the foundation of persistent, valuable output. These outputs are derived from deliberate practice and the effort of burning mental calories. Their outputs are either based on practical application, such as Jay Kreps' open-source project Kafka and Confluent; or are generated after consolidating and sharing their learnings with others, such as Brendan Gregg's technical presentations, documents/videos, books, and Brad Traversy's teaching videos, etc. In other words, they have consistently engaged in active and effective learning at the 5-7 levels of the learning pyramid. Furthermore, their learning outputs can also provide value to users, aligning with customer value (Customer Value). With users, there comes feedback and metrics. Remember, learning that includes feedback and metrics is called closed-loop learning, which allows for continuous improvement and enhancement; conversely, learning without feedback and metrics cannot be improved.

By now, you should also realize that it's quite simple to showcase a reading list or present a skills map. Reading books or attending classes also isn’t challenging. However, formulating a comprehensive technical growth strategy for the next 5 to 10 years, and then deriving annual segmented implementations based on that strategy (especially output plans), followed by strict adherence—this is indeed a challenging endeavor. It requires extensive practical training and deep reflection, burning significant mental calories! But this is a law of evolution set by nature; growing to become a true technical elite, just like becoming a top athlete, requires burning a matching amount of calories. To become a true technical expert necessitates generating social value in line with one's outputs; only then can society evolve normally. When you champion societal evolution, society will reciprocate. If not, society will stagnate.

## 4. The Birth of Strategic Thinking

![Thinking Cycle and Opportunity Points](https://oss.javaguide.cn/p3-juejin/dc87167f53b243d49f9f4e8c7fe530a1~tplv-k3u1fbpfcp-zoom-1.png)

Typically, when fresh graduates first enter a corporate job, their thinking is often unit-based—days, weeks, or months. They usually focus on learning a particular technology today and another language tomorrow, rarely contemplating year-long or longer goals. This phase consists of an unclear and ignorant mindset where capturing opportunities is a significant challenge.

After working for three years, perceptive individuals usually adapt their thinking cycles to the one-year frame, formulating and implementing annual plans. This stage focuses on talent and skill competition to capture small opportunities.

By the five-year mark, some astute individuals develop a certain level of courage and insight. They begin to implement plans on a 3-5 year cycle, actively positioning themselves to seize medium-scale opportunities.

By the ten-year point, highly perceptive individuals can start to see patterns and changes in rules, such as industry development models, and talent growth patterns, leading to the birth of strategic thinking. They will then devise and implement their strategic plans on 5-10 year cycles, actively positioning themselves to seize medium to large opportunities. Brendan Gregg, Jay Kreps, and Brad Traversy all belong to this stage.

There are, of course, quite a few extraordinary talents who can perceive the essence of the times and human nature. Their thinking spans over their lifetime or even longer. However, these transcendents are beyond the scope of this article.

## 5. Recommendations

**1. Plan your strategy over a period of 5-10 years.**

Currently, university graduates are generally around 22-23 years old. After about ten years in the workplace, when you reach about 32-33 years old, you should have gained enough experience over a decade to develop a rather profound insight into yourself and the world around you (your industry and field). **If by this age you are still bewildered, chasing after this and that, it can only be said that your courage and perspective are quite limited.** In today's fiercely competitive IT industry, the possibility of being laid off by 35 years old may very well loom.

With strategic thinking in place, you should plan your strategy over a 5-10 year span. Taking Brendan Gregg, Jay Kreps, and Brad Traversy as examples, **if one truly wants to accomplish something significant in life, the commitment period generally spans a decade.** Starting from age 33, you essentially have three decades to work with, as most people become less capable of achieving significant endeavors beyond the age of 60. If your perception is not as keen and you only begin planning at 40, you would be left with approximately two decades. If planned effectively, these 20-30 years could lead to considerable accomplishments. Otherwise, it is likely that you may accomplish little or spend your life helping others succeed.

**2. Concentrate your energy.**

Considering that one's career span generally covers about two to three decades, you'll realize that life is quite short. At this juncture, you should focus all your energy on realizing your ten-year strategy, leaving no time to squander on idle online chatter or fruitless debates.

**3. Segment your yearly implementation plans, especially your output plans.**

Having established a ten-year strategic direction, the next step is to formulate annual segmented implementation plans, focusing particularly on output plans. These plans should primarily operate at levels 5/6/7 of the learning pyramid. **Output should result from deliberate practice plus burning calories, ensuring both your body and brain maintain a consistent caloric expenditure.**

**4. Generate valuable output to form a positive feedback loop.**

Output should have customer value, allowing for personal growth (your own evolution) and benefit others (propelling societal progress). This creates a **feedback loop with users and metrics**, enabling continuous improvement and enhancement of your learning.

**5. Less is more.**

Delve deeply into one (or a few related) fields. All segmented plans should closely align with your strategy. Restrain internal desires to avoid spreading yourself thin and be misled by the clamorous world.

**6. Document both your strategic direction and segmented plans, regularly reviewing and optimizing them.**

**7. Maintain resolve and consistent effort.**

All paths may be winding, and success will not come in a straight line. Strategic directions and segmented plans often need to be adjusted as necessary, especially in the beginning; however, they should ultimately converge. If changes are constant without settling, it signifies a lack of strategic resolve, which is a significant issue that needs to be thoughtfully addressed.

You can reference others' growth strategies, but avoid trying to mimic them overly; you have your own unique qualities; **you should become a one-of-a-kind version of yourself.**

Once your strategic direction and segmented plans are clarified, the next step is to execute step by step, adhering steadfastly over the long haul.

**8. Slow is fast.**

The achievement of strategic goals requires time and patient cultivation, much like growing trees; remember, **slow is fast.** During anxious periods, silently affirm teachings from Wang Yangming's **Instructions for Cultivating One's Mind**:

> Setting high aspirations and working hard is like growing a tree. At first, there are only small roots; the trunk has not emerged; only once the trunk develops can branches begin to grow; once branches grow, leaves will follow, and then flowers and fruits will emerge. When first planting, just focus on nurturing and watering. Do not dwell on when branches will grow or when flowers will bloom or fruits will be borne. What good does worrying do? Just persist in the nurturing efforts, and you need not fear without producing branches, leaves, or fruit.
>
> Translation:
>
> Achieving strategic goals is akin to planting trees. Initially, just a small root grows, and the trunk hasn't yet formed; once the trunk is established, branches begin to sprout slowly; only after branches sprout will flowers and fruits follow. At the beginning of planting, just focus on nurturing and watering, without constantly fretting over when branches will appear, when flowers will bloom, or when fruits will form. What benefit is there in worrying? As long as you persist in your nurturing efforts, need you fear the absence of branches, leaves, or fruit?

<!-- @include: @article-footer.snippet.md -->
