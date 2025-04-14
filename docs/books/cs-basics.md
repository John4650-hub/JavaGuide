---
title: Must-Read Classic Books on Computer Fundamentals
category: Computer Books
icon: computer
head:
  -   - meta
      - name: keywords
        content: Selected Computer Fundamentals Books
---

Considering that many students prefer watching videos, in this section, I will not only recommend books but also some video tutorials and projects from major universities that I think are great.

## Operating Systems

**Why learn about operating systems?**

**From the perspective of personal skill enhancement**, many concepts and classic algorithms in operating systems can be found in the various tools or frameworks we use in daily development. For example, the caching used in the systems we develop (like Redis) is very similar to the cache in operating systems. There are many types of caches in the CPU, but most are designed to address the disparity between CPU processing speed and memory processing speed. We can also think of memory as a high-speed cache for external storage; when a program runs, we copy data from external storage to memory, which significantly speeds up processing due to the much higher processing speed of memory compared to external storage. Similarly, the Redis cache we use is designed to solve the problem of the disparity between program processing speed and the speed of accessing conventional relational databases. Caches generally ensure that the data in them is frequently accessed according to the principle of locality (the 2-8 principle) and corresponding eviction algorithms. The Redis cache we commonly use often follows the 2-8 principle, and many eviction algorithms are similar to those in operating systems. Speaking of the 2-8 principle, we must mention the hit rate, which is a common concept for all caches. Simply put, it refers to how much of the data you want to access can be found directly in the cache. A high hit rate generally indicates that your cache design is reasonable, and the system's processing speed is relatively fast.

**From the interview perspective**, especially for campus recruitment, there is a significant emphasis on knowledge of operating systems.

**In short, learning about operating systems can deepen your thinking and understanding of technology, and knowledge in this area is essential for interviews.**

If you want to systematically learn about operating systems, the most authoritative and hardcore book is **[“Operating System Concepts”](https://book.douban.com/subject/33463930/)**. You can also pair it with **[“Computer Systems: A Programmer's Perspective”](https://book.douban.com/subject/1230413/)** to deepen your understanding of the essence of computer systems, which is delightful!

![](https://oss.javaguide.cn/github/javaguide/booksimage-20201012191645919.png)

Additionally, a new domestic book on operating systems released last year is also quite good: **[“Modern Operating Systems: Principles and Implementation”](https://book.douban.com/subject/35208251/)** (a collaborative work by Professor Xia and Professor Chen's team, highly recommended).

![](https://oss.javaguide.cn/github/javaguide/books/20210406132050845.png)

If you prefer hands-on experience and are resistant to theoretical knowledge, I recommend you check out **[“30 Days to Create Your Own Operating System”](https://book.douban.com/subject/11530329/)**, which will guide you step by step in writing an operating system.

Learning from books alone is never enough; practical experience is essential! I strongly recommend that CS majors engage in more hands-on practice!!!

![](https://oss.javaguide.cn/github/javaguide/booksimage-20220409123802972.png)

Other related book recommendations:

- **[“Operating Systems: Design and Implementation”](https://book.douban.com/subject/1422377/)**: This book not only provides a detailed analysis of the principles of operating systems but also guides you step by step with rich example code to write an operating system framework with basic functionalities using C and assembly language.
- **[“Modern Operating Systems”](https://book.douban.com/subject/3852290/)**: The content is quite good, but the translation is average. If you plan to read this book thoroughly, it is recommended to complete the exercises at the end of each chapter.
- **[“Operating Systems: Three Easy Pieces”](https://book.douban.com/subject/26745156/)**: The author of this book graduated from Peking University and was a senior engineer at Baidu. After retaking the operating systems course in college, he conducted in-depth research on operating systems and wrote this book.
- **[“Deep Dive into Linux Operating System”](https://book.douban.com/subject/25743846/)**: Following the content of this book will give you a clear understanding of how to create a complete GNU/Linux system.
- **[“Operating System Design and Implementation”](https://book.douban.com/subject/2044818/)**: An authoritative textbook on operating
