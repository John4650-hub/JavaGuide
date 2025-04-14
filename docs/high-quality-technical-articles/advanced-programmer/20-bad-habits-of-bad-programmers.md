---
title: 20 Bad Habits of Terrible Programmers
category: Selected Technical Articles
author: Kaito
tag:
  - Leveling Guide
---

> **Recommendation**: A very practical article by Kaito!
>
> **Original link:** <https://mp.weixin.qq.com/s/6hUU6SZsxGPWAIIByq93Rw>

I believe you have encountered a type of programmer: **they appear particularly professional in writing code, documentation, or communicating with others**. Every time I meet such individuals, I wonder how they manage it.

As my work experience has grown, I have gradually summarized some insights: they maintain some seemingly minor but excellent habits, which reflect the basic qualities of an outstanding programmer.

Today, however, let's take a different perspective and examine what bad habits a terrible programmer has. As long as we can avoid these issues, we can gradually move toward becoming an excellent programmer.

## 1. Non-standard Spelling of Technical Terms

In personal resumes or technical documentation, I often see irregular spellings of technical terms, such as JAVA, javascript, python, MySql, Hbase, restful.

The correct spellings should be Java, JavaScript, Python, MySQL, HBase, RESTful. Don’t underestimate this issue; many interviewers might dismiss your resume due to this.

## 2. Inconsistent Mixing of Chinese and English in Documents

Using English punctuation in Chinese descriptions, full-width characters for English and numbers, and not adding spaces between Chinese and English or numbers, etc.

Many overlook the importance of adding a "space" between Chinese and English or numbers to make reading more comfortable. My previous articles adhered to these details in formatting.

## 3. Important Logic Without Comments or Overly Verbose Comments

For complex and critical logic, many programmers neglect to write comments. While they can understand their code, others cannot. Alternatively, they might write comments that are overly verbose and lack logical structure.

Important logic requires concise and clear comments. If the code is simple enough to understand at a glance, comments may not be necessary.

## 4. Writing Complex and Lengthy Functions

A function spans hundreds of lines, a file contains thousands of lines of code, and complex functions are not divided, making the code increasingly difficult to maintain, leading to a situation where no one dares to modify it.

Basic design patterns should still be followed, such as the Single Responsibility Principle (SRP) where a function should only perform one task, and the Open/Closed Principle (OCP) where it should be open for extension but closed for modification.

If the function logic is indeed complex, at least ensure the main logic is clear enough.

## 5. Not Reading Official Documentation, Only Junk Blogs

Many people do not check the official documentation first when encountering a problem but instead are keen to look at junk blogs with plagiarized content full of errors.

In fact, many software official documents are well-written, with answers to common questions readily available. Taking the time to read the official documentation is a hundred times better than looking at junk blogs; it’s essential to cultivate the habit of consulting official documentation.

## 6. Advocating the Futility of Fundamental Knowledge

Some people pursue ever-changing open-source projects and frameworks every day but refuse to spend time understanding underlying principles. They can resolve common issues but are helpless in the face of slightly more complex problems.

Many sophisticated architectural designs originate from the fundamentals. Consider how much evolution has occurred over the years in computer architecture, operating systems, and network protocols, encountering countless complex problems along the way. Understanding these problem-solving approaches makes it easier to grasp higher-level technologies.

## 7. Eagerness to Show Off Technical Skills

Some people constantly flaunt "high-end" technical terms, fearing others don't realize the advanced technologies they’ve learned. However, when asked for details, they become speechless.

## 8. Inability to Accept Criticism

When others question their designed solutions, they only retort instead of rationally analyzing the pros and cons, failing to engage in discussions with a learning mindset.

These individuals often feel capable after learning a bit, unaware that it’s simply due to their lack of exposure.

## 9. Non-standard API Protocols

Setting API protocols based solely on verbal communication without providing a formal documentation, then discovering during testing that the output differs from what was agreed upon, or altering the protocol without informing the counterpart results in a poor collaborative experience.

## 10. Struggling Alone with Problems

A common mistake among novice programmers is struggling with issues on their own, resulting in no output until the deadline, only to reveal unresolved problems when asked by a supervisor.

Timely feedback on issues is crucial for personal accountability and team responsibility.

## 11. All Talk, No Action

They can speak eloquently about technical solutions but falter when it comes to writing code, showcasing a classic case of having great aspirations but lacking practical capabilities.

## 12. Disorganized Expression and Lack of Empathy

During discussions, they fail to provide background information and dive straight into solutions, leaving others confused; when asked to elaborate, they struggle to clarify.

Learning to communicate and express oneself is foundational to collaboration.

## 13. Passive and Reluctant to Think

When facing problems, they do not Google for solutions and instead ask others without considering the issue, showcasing a tendency to be reliant on others.

Everyone's time is precious; people prefer queries accompanied by one’s own thought process. This avoids many basic issues and enhances the quality of communication.

## 14. Frequently Repeating Mistakes

After an issue arises, they promise to be more careful next time, yet the same problems recur, indicating a lack of personal accountability and ultimately reflecting an attitude problem.

## 15. Ignoring Scalability When Adding Features

When adding new features, they only focus on a specific area of business without considering the overall scalability of the system, leading to excessive code bloat.

It's essential to analyze requirements and potential future changes to design more universal solutions, thereby reducing later development costs.

## 16. Not Self-testing APIs and Failing to Log Errors

Collaborating without self-testing their developed APIs and then claiming there's no logging when issues arise results in extremely low collaboration efficiency.

## 17. Non-standard Code Submission Practices

Many people submit code without writing a description or writing meaningless ones, especially when they’ve made minimal changes. This can lead to increased costs when tracing back problems.

Establishing code submission standards ensures that you do not make casual code modifications with every submission.

## 18. Directly Modifying Production Environment Databases

Directly connecting to and modifying production databases creates risks, with cases of SQL UPDATE/DELETE missing WHERE conditions, leading to data accidents.

Always exercise extreme caution when modifying production databases; it is advisable to get a colleague to review the code before proceeding.

## 19. Writing Code Without Clarifying Requirements

Many programmers start coding immediately upon receiving a requirement without much thought, which can lead to misunderstandings and unnecessary rework.

Taking extra time to clarify requirements can avoid many unreasonable problems.

## 20. Lack of Documentation for Important Designs

Failing to document significant designs and only providing verbal descriptions during handovers leads to the loss of key information.

Sometimes understanding a design plan is more efficient through a good document than sifting through hundreds of lines of code.

## Conclusion

How many of these bad habits have you encountered? Or do you know anyone like this?

I believe proactively avoiding these issues is essential for becoming an excellent programmer. These habits can be summarized into four key aspects:

- Good programming discipline
- A humble attitude toward learning
- Effective communication and expression
- Emphasis on teamwork

While it might be challenging to quickly acquire the professional skills of an excellent programmer, cultivating these basic professional qualities can be achieved in a short period.

I hope both you and I can improve ourselves where necessary and strive for excellence.
