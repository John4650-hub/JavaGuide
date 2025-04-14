---
title: Summary of Common MySQL Interview Questions
category: Database
tag:
  - MySQL
  - Big Company Interviews
head:
  -   - meta
      - name: keywords
        content: MySQL basics, MySQL architecture, MySQL storage engines, MySQL query cache, MySQL transactions, MySQL locks, etc.
  -   - meta
      - name: description
        content: An article summarizing common knowledge points and interview questions about MySQL, covering MySQL basics, MySQL architecture, MySQL storage engines, MySQL query cache, MySQL transactions, MySQL locks, etc.
---

<!-- @include: @small-advertisement.snippet.md -->

## MySQL Basics

### What is a Relational Database?

As the name suggests, a relational database (RDB) is a type of database built on the relational model. The relational model indicates the relationships between the data stored in the database (one-to-one, one-to-many, many-to-many).

In a relational database, our data is stored in various tables (for example, a user table), with each row in the table storing a piece of data (for example, information about a user).

![Relational Database Table Relationships](https://oss.javaguide.cn/java-guide-blog/5e3c1a71724a38245aa43b02_99bf70d46cc247be878de9d3a88f0c44.png)

Most relational databases use SQL to manipulate the data within them. Additionally, most relational databases support the four major properties of transactions (ACID).

**What are some common relational databases?**

MySQL, PostgreSQL, Oracle, SQL Server, SQLite (the local chat record storage in WeChat uses SQLite), etc.

### What is SQL?

SQL is a Structured Query Language specifically designed for interacting with databases, providing a simple and effective way to read and write data from a database.

Almost all mainstream relational databases support SQL, making it highly applicable. Some non-relational databases also support SQL or use a query language similar to SQL.

SQL can help us:

- Create databases, tables, and fields;
- Add, delete, modify, and query data in the database;
- Create views, functions, and stored procedures;
- Perform simple data analysis on the data in the database;
- Work with Hive and Spark SQL for big data;
- Work with SQLFlow for machine learning;
- ……

### What is MySQL?

![](https://oss.javaguide.cn/github/javaguide/csdn/20210327143351823.png)

**MySQL is a relational database primarily used for the persistent storage of some data in our systems, such as user information.**

Since MySQL is open-source, free, and relatively mature, it is widely used in various systems. Anyone can download it under the GPL (General Public License) and modify it according to their needs. The default port number for MySQL is **3306**.

### What are the advantages of MySQL?

This question essentially asks why MySQL is so popular.

MySQL has the following advantages:

1. Mature, stable, and feature-rich.
1. Open-source and free.
1. Abundant documentation, including detailed official documentation and many high-quality articles for reference and learning.
1. Ready to use, easy to operate, and low maintenance costs.
1. Good compatibility, supporting common operating systems and various programming languages.
1. Active community and complete ecosystem.
1. Excellent transaction support; the InnoDB storage engine uses REPEATABLE-READ by default without any performance loss, and the REPEATABLE-READ isolation level implemented by InnoDB can actually solve the phantom read problem.
1. Supports sharding, read-write separation, and high availability.

## MySQL Field Types

MySQL field types can be simply divided into three categories:

- **Numeric Types**: Integer (TINYINT, SMALLINT, MEDIUMINT, INT, and BIGINT), Floating-point (FLOAT and DOUBLE), Fixed-point (DECIMAL)
- **String Types**: CHAR, VARCHAR, TINYTEXT, TEXT, MEDIUMTEXT, LONGTEXT, TINYBLOB, BLOB, MEDIUMBLOB, and LONGBLOB, etc. The most commonly used are CHAR and VARCHAR.
- **Date and Time Types**: YEAR, TIME, DATE, DATETIME, and TIMESTAMP, etc.

The following image is not drawn by me; I forgot where I saved it from, but it summarizes quite well.

![Summary of Common MySQL Field Types](https://oss.javaguide.cn/github/javaguide/mysql/summary-of-mysql-field-types.png)

MySQL has many field types, and I will select some frequently used and commonly asked field types in interviews to introduce in detail in the form of interview questions. Unless otherwise specified, the focus is on the InnoDB storage engine.

Additionally, I recommend reading Chapter 4 of "High-Performance MySQL (3rd
