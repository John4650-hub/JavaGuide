---
title: Summary of NoSQL Fundamentals
category: Database
tag:
  - NoSQL
  - MongoDB
  - Redis
---

## What is NoSQL?

NoSQL (Not Only SQL) refers to non-relational databases that primarily target key-value, document, and graph data storage. Moreover, NoSQL databases naturally support distributed features, data redundancy, and data partitioning, aiming to provide scalable, high-availability, and high-performance data storage solutions.

A common misconception is that NoSQL databases or non-relational databases cannot store relational data well. NoSQL databases can store relational data—they just have a different storage method compared to relational databases.

Examples of NoSQL databases include: HBase, Cassandra, MongoDB, Redis.

![](https://oss.javaguide.cn/github/javaguide/database/mongodb/sql-nosql-tushi.png)

## What are the differences between SQL and NoSQL?

|                     | SQL Databases                                                                                                                                              | NoSQL Databases                                                                                                                                                                                      |
| :------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Data Storage Model  | Structured storage with tables that have fixed rows and columns                                                                                            | Unstructured storage. Documents: JSON documents, Key-Value: Key-Value pairs, Wide Column: Tables with rows and dynamic columns, Graph: Nodes and edges                                               |
| Development History | Developed in the 1970s, focusing on reducing data duplication                                                                                              | Developed in the late 2000s, focusing on improving scalability and reducing storage costs for massive amounts of data                                                                                |
| Examples            | Oracle, MySQL, Microsoft SQL Server, PostgreSQL                                                                                                            | Documents: MongoDB, CouchDB; Key-Value: Redis, DynamoDB; Wide Column: Cassandra, HBase; Graph: Neo4j, Amazon Neptune, Giraph                                                                         |
| ACID Properties     | Provides Atomicity, Consistency, Isolation, and Durability (ACID) properties                                                                               | Generally does not support ACID transactions, making trade-offs for scalability and high performance; some, like MongoDB, support them, but differently than MySQL.                                  |
| Performance         | Performance typically depends on the disk subsystem. To achieve optimal performance, optimizing queries, indexes, and table structures is often necessary. | Performance is usually determined by the size of the underlying hardware cluster, network latency, and the application calling.                                                                      |
| Scalability         | Vertical (scaling up using more powerful servers), read-write separation, database sharding                                                                | Horizontal (scaling out by adding servers, usually based on sharding mechanisms)                                                                                                                     |
| Use Cases           | Data storage for typical enterprise-level projects                                                                                                         | Wide-ranging uses, for example, graph databases support analysis and traversing relationships between connected data; key-value databases can handle massive data scaling and extreme state changes. |
| Query Language      | Structured Query Language (SQL)                                                                                                                            | Data access syntax may vary by database                                                                                                                                                              |

## What are the advantages of NoSQL databases?

NoSQL databases are particularly suitable for many modern applications, such as mobile, web, and gaming applications, which require flexible, scalable, high-performance, and powerful databases to provide excellent user experiences.

- **Flexibility:** NoSQL databases often provide flexible architectures for faster and more iterative development. Their flexible data models make NoSQL databases ideal for semi-structured and unstructured data.
- **Scalability:** NoSQL databases are generally designed to scale horizontally by utilizing distributed hardware clusters rather than vertically scaling by adding expensive and powerful servers.
- **High Performance:** NoSQL databases are optimized for specific data models and access patterns, achieving higher performance compared to trying to accomplish similar functions with a relational database.
- **Powerful Features:** NoSQL databases offer powerful APIs and data types designed specifically for their respective data models.

## What types of NoSQL databases are there?

NoSQL databases can primarily be divided into the following four types:

- **Key-Value:** Key-value databases are a simpler type of database where each item consists of a key and a value. This is a very flexible type of NoSQL database because applications have complete control over the content stored in the value field, with no restrictions. Redis and DynamoDB are two popular key-value databases.
- **Document:** Data in document databases is stored in documents resembling JSON (JavaScript Object Notation) objects, which are very clear and intuitive. Each document contains paired fields and values. These values can typically be of various types, including strings, numbers, booleans, arrays, or objects, and their structure often aligns with the objects developers use in code. MongoDB is a very popular document database.
- **Graph:** Graph databases are designed to make it easy to build and run applications that work with highly connected datasets. Typical use cases for graph databases include social networks, recommendation engines, fraud detection, and knowledge graphs. Neo4j and Giraph are two well-known graph databases.
- **Wide Column:** Wide-column store databases are well-suited for scenarios requiring the storage of massive amounts of data. Cassandra and HBase are two popular wide-column storage databases.

The image below is sourced from [Microsoft's official documentation | Relational Data vs. NoSQL Data](https://learn.microsoft.com/en-us/dotnet/architecture/cloud-native/relational-vs-nosql-data).

![NoSQL Data Model](https://oss.javaguide.cn/github/javaguide/database/mongodb/types-of-nosql-datastores.png)

## References

- What is NoSQL? - MongoDB Official Documentation: <https://www.mongodb.com/zh-cn/nosql-explained>
- What is NoSQL? - AWS: <https://aws.amazon.com/cn/nosql/>
- NoSQL vs. SQL Databases - MongoDB Official Documentation: <https://www.mongodb.com/zh-cn/nosql-explained/nosql-vs-sql>

<!-- @include: @article-footer.snippet.md -->
