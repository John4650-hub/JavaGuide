---
title: Must-Read Classic Books on Databases
category: Computer Books
icon: database
head:
  -   - meta
      - name: keywords
        content: Selected Database Books
---

## Database Fundamentals

If you find the books on database fundamentals quite dry and cannot stick with it, I recommend that you first look at some good videos, such as Beijing Normal University's [“Principles of Database Systems”](https://www.icourse163.org/course/BNU-1002842007) and Harbin Institute of Technology's [“Database Systems (Part 2): Management and Technology”](https://www.icourse163.org/course/HIT-1001578001).

The instructor for [“Principles of Database Systems”](https://www.icourse163.org/course/BNU-1002842007) is very detailed, and the assignments for each section are closely related to the knowledge being taught, with many supplementary experiments afterwards.

![](https://oss.javaguide.cn/github/javaguide/books/up-e113c726a41874ef5fb19f7ac14e38e16ce.png)

If you prefer hands-on activities and resist theoretical knowledge, I recommend you check out [“How to Develop a Simple Database”](https://cstack.github.io/db_tutorial/); this project will guide you step by step in writing a simple database.

![](https://oss.javaguide.cn/github/javaguide/books/up-11de8cb239aa7201cc8d78fa28928b9ec7d.png)

There is also a simplified database implemented in Java on GitHub by some experts, which is quite detailed, and those interested can take a look. Address: [https://github.com/alchemystar/Freedom](https://github.com/alchemystar/Freedom).

Aside from the Java implementation, **[db_tutorial](https://github.com/cstack/db_tutorial)** is a project by an expert abroad that is written in C language; friends can check that out as well.

**As long as you make good use of search engines, you can find various language implementations of database toys.**

![](https://oss.javaguide.cn/github/javaguide/books/up-d32d853f847633ac7ed0efdecf56be1f1d2.png)

**Learning from books alone is shallow; you must practice to fully understand! I strongly recommend that friends majoring in CS should practice more!!!**

### "Database System Concepts"

[“Database System Concepts”](https://book.douban.com/subject/10548379/) covers the complete set of concepts related to database systems. The knowledge system is clear and it is a very classic textbook for learning database systems! It is not a reference book!

![](https://oss.javaguide.cn/github/javaguide/booksimage-20220409150441742.png)

### "Database System Implementation"

If you are interested in researching the underlying principles of MySQL, I recommend you read [“Database System Implementation”](https://book.douban.com/subject/4838430/) first.

![](https://oss.javaguide.cn/github/javaguide/books/database-system-implementation.png)

Whether it’s MySQL or Oracle, their overall framework is quite similar; the differences are mainly in their internal implementations—such as the data structures used for database indexing and the implementation methods of the storage engines.

This book is somewhat awkward in translation in certain places, so if you can read the English version, it is recommended to start with that.

“Database System Implementation” is a textbook from Stanford, and there's also a precursor course book, [“Database System Fundamentals”](https://book.douban.com/subject/3923575/), which can introduce you to databases.

## MySQL

The data for our website or APP needs to be stored in a database.

In general, MySQL is often used in enterprise project development. If you want to learn MySQL, you can refer to the following three books:

- **[“MySQL Must-Know Must-Read”](https://book.douban.com/subject/3354490/)**: Very thin! Extremely suitable for beginners learning MySQL; it is a great introductory textbook.
- **[“High-Performance MySQL”](https://book.douban.com/subject/23008813/)**: A classic work in the MySQL field! A must-read for learning MySQL! It belongs to the advanced content and mainly teaches you how to use MySQL better. It contains both theory and practice! If you don’t have time to read it all, I suggest you carefully review Chapter 5 (Creating High-Performance Indexes) and Chapter 6 (Query Performance Optimization).
- **[“MySQL Technical Insider”](https://book.douban.com/subject/24708143/)**: If you want to delve into the MySQL storage engine, this book is definitely the right choice!

![](https://oss.javaguide.cn/github/javaguide/books/up-3d31e762933f9e50cc7170b2ebd8433917b.png)

For videos, you can check out Donglijian's [“MySQL Database Tutorial Video”](https://www.bilibili.com/video/BV1fx411X7BD). This video basically covers all the introductory information related to MySQL.

Additionally, I strongly recommend **[“How MySQL Works”](https://book.douban.com/subject/35231266/)**; the content is very suitable for interview preparation. It discusses many details without being boring, and the material is outstanding!

![](https://oss.javaguide.cn/github/javaguide/csdn/20210703120643370.png)

## PostgreSQL

Like MySQL, PostgreSQL is also an open-source, free and powerful relational database. PostgreSQL's slogan is “**The World's Most Advanced Open Source Relational Database**”.

![](https://oss.javaguide.cn/github/javaguide/books/image-20220702144954370.png)

In recent years, due to various excellent new features of PostgreSQL, more and more projects are replacing MySQL with PostgreSQL.

If you are still unsure whether to try PostgreSQL, I suggest you check out this Zhihu topic: [What are the advantages of PostgreSQL compared to MySQL? - Zhihu](https://www.zhihu.com/question/20010554).

### "PostgreSQL Guide: Inside Exploration"

[“PostgreSQL Guide: Inside Exploration”](https://book.douban.com/subject/33477094/) mainly introduces the internal working principles of PostgreSQL, including the logical organization and physical implementation of database objects, as well as the architecture of processes and memory.

When I first started working, I needed to use PostgreSQL and read about 1/3 of the content, which felt quite good.

![](https://oss.javaguide.cn/github/javaguide/books/PostgreSQL-Guide.png)

### "PostgreSQL Technical Insider: In-Depth Exploration of Query Optimization"

[“PostgreSQL Technical Insider: In-Depth Exploration of Query Optimization”](https://book.douban.com/subject/30256561/) mainly discusses some technical implementation details regarding query optimization in PostgreSQL, allowing you to gain a deeper understanding of the PostgreSQL query optimizer.

![“PostgreSQL Technical Insider: In-Depth Exploration of Query Optimization”](https://oss.javaguide.cn/github/javaguide/books/PostgreSQL-TechnologyInsider.png)

## Redis

**Redis is a database developed in C language**, but unlike traditional databases, **the data of Redis is stored in memory**, meaning it is an in-memory database and thus has very fast read and write speeds. As a result, Redis is widely used in caching.

If you want to learn Redis, I highly recommend the following two books:

- [“Redis Design and Implementation”](https://book.douban.com/subject/25900156/): This primarily covers theoretical knowledge related to Redis and is quite comprehensive. I wrote an article before [“Seven Years Ago, at 24, I Published a Redis Classic”](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247507030&idx=1&sn=0a5fd669413991b30163ab6f5834a4ad&chksm=cea1939df9d61a8b93925fae92f4cee0838c449534e60731cfaf533369831192e296780b32a6&token=709354671&lang=zh_CN&scene=21#wechat_redirect) to introduce this book.
- [“Redis Core Principles and Practices”](https://book.douban.com/subject/26612779/): Mainly analyzes important knowledge points of Redis through source code, such as various data structures and advanced features.

![“Redis Design and Implementation” and “Redis Core Principles and Practices”](https://oss.javaguide.cn/github/javaguide/books/redis-books.png)

Additionally, [“Redis Development and Operations”](https://book.douban.com/subject/26971561/) is also quite good, offering both basic introductions and frontline development and operations experience sharing.

![“Redis Development and Operations”](https://oss.javaguide.cn/github/javaguide/books/redis-kaifa-yu-yunwei.png)
