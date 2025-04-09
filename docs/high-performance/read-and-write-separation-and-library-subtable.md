I don't...
title: Read-write separation and breakdown of the partition
Category: High performance
Head:

- Meta.
- Name: keywords
  Contact: Read-write Separation, Split-Books, Main Copy
- Meta.
- Name: description
  Content: Reading and writing separation is primarily intended to spread the reading and writing operations of the database to different database nodes. This would allow for a small increase in writing performance and a significant increase in reading performance. Reading and writing separation is based on master copying, and MySQL is dependent on binlog for copying. The repository is the decentralization of data from databases to different databases. The tabulation is a split of the individual table data, either vertically or horizontally. The introduction of a split library will require a systematic solution for service, distributed ID, and inability to work.
  I don't...

# Read and write separation

# What's reading and writing apart?

As you can imagine, based on the name that is separated by reading and writing, we can see: **reading and writing separation is primarily intended to spread the reading and writing of databases to different database nodes.** This would enable a small increase in writing performance and a significant increase in reading performance.

I have drawn a simple picture to help small partners who are less clear about how to read and write.

![Read and write separation diagram](https://oss.javaguide.cn/github/javaguide/high-perform/read-and-write-security-and-library-subtable/read-and-write-security.png)

As a general rule, we choose one main source, that is, one primary database to write and the others to read from. The data will be synchronized between the main library and the library to ensure the accuracy of the data from the library. Such a structure would be simpler to achieve and would be consistent with the system's less read and less write features.

# How do you separate reading and writing?

Whether or not the specific realization of reading and writing separation is used, the desire to achieve a reading and writing separation typically consists of the following steps:

1. Deployment of multiple databases, one of which will be selected as the main database and one or more of which will be used as a source of data.
1. The process of ensuring real-time synchronization between the main database and the data from the database, which we often call **the master copy**.
1. The system refers writing requests to the main database for processing and requests for reading to be processed from the database.

When it comes to the project itself, there are two common ways:

**1. Representation**

[Agently achieves reading and writing separation](https://oss.javaguide.cn/github/javaguide/high-perform/read-and-write-security-and-library-subtable/read-and-write-security-proxy.png)

We can add an agent layer to the application and data. All data requests for applications are processed at the proxy level, which is responsible for separating requests for reading and writing and for routing them into the corresponding database.

The intermediates providing similar functions are **MySQL Router** (official, MySQL Proxy alternative), **Atlas** (based on MySQL Proxy), **MaxScale**, **MyCat**.

With respect to MySQL Router, mention more: In MySQL 8.2 version, MySQL Router can automatically distinguish between the reading/writing of the database and the routing of these operations to the correct instance. This is a valuable function that optimizes database performance and scalability without any change in the application. More specific information can be found in the official blog: [MySQL 8.2 – Transparent read/write splitting](https://blogs.oracle.com/mysql/post/mysql-82-transparent-readwrite-splitting).

**2. Component approach**

In this way, we can help us to read and write requests by introducing third-party components.

That's one of the ways I'd rather recommend. This approach is currently most used in various Internet companies, and there are many relevant practical cases. If you want to do so, it is very convenient to recommend the use of `sharding-jdbc` and the direct introduction of jar packages. At the same time, there have been significant cost savings.

You can find [sharding-jdbc on the operation of reading and writing separation in shardingshere](https://shardingsphere.apache.org/document/legal/3.x/document/cn/manual/sarding-jdbc/usage/read-write-splitting/).

# What's the principle of Master copying?

MySQL binlog (binary log, or binary log file) mainly records all changes to data in MySQL (all DDL and DML statements executed in the database). So we can synchronize the data from the main library with the MySQL binlog log.

This is the more specific and detailed process (photo from: [MySQL Master-Slave Presentation on the Same Machine](https://www.toptal.com/mysql/mysql-master-slave-registration-tutorial)):

(https://oss.javaguide.cn/java-guide-blog/788816271d3ab5224bfd5ad3086c1a0f.png)

1.
