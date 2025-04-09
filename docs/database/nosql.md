I don't...

# Title: NoSQL Basic Knowledge Summary

## Category: Database

### Tag:

- NoSQL
- MongoDB
- Redis
  I don't...

## NoSQL is What?

NoSQL (not Only SQL) refers in general to non-relational databases, mainly for key-value, document, and graph-type data storage. Furthermore, the NoSQL database is designed to support distribution, data redundancy, and data fragmentation, and is intended to provide scalable, high-availability, high-performance data storage solutions.

A common misunderstanding is that the NoSQL database or non-relational database does not store relational data well. NoSQL databases can store relational data, but not in the same way as relational databases.

### NoSQL Database Representatives: HBase, Cassandra, MongoDB, Redis.

![NoSQL Data Model](https://oss.javaguide.cn/github/javaguide/database/mongodb/sql-nosql-tushi.png)

## What's the Difference Between SQL and NoSQL?

| SQL Database                                                                                                                                                                                                                                                                                                | NoSQL Database                                                                                                                                                                                                                                                         |
| :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Data storage model                                                                                                                                                                                                                                                                                          | Structured storage, tables with fixed rows and columns                                                                                                                                                                                                                 |
| In the 1970s, the focus was on reducing data repetition. Developed in the late 2000s, with emphasis on increasing scalability and reducing the cost of storing large-scale data                                                                                                                             | Example: Oracle, MySQL, Microsoft SQL Server, PostgreSQL                                                                                                                                                                                                               |
| The ACID Properties                                                                                                                                                                                                                                                                                         | Provides atomicity, consistency, isolation, and durability (ACID) properties                                                                                                                                                                                           |
| Performance                                                                                                                                                                                                                                                                                                 | Usually depends on the disk system. Optimization of queries, indexes, and table structures is usually required to obtain optimal performance. Performance is usually determined by the size of the underlying hardware cluster, network delays, and application calls. |
| Data storage for projects at the general enterprise level, data storage for a wide range of purposes, such as the relationship between graph database support for analysis and cross-linking of data, and the ability of the key-value database to handle large data extensions and very high state changes | Syntax: Structured Query Language (SQL)                                                                                                                                                                                                                                |

## What Are the Advantages of the NoSQL Database?

NoSQL databases are well suited to many modern applications, such as mobile, web, and games, which require flexible, scalable, high-performance, and powerful databases to provide an excellent user experience.

- **Flexibility:** The NoSQL database usually provides a flexible framework for faster and more iterative development. Flexible data models make the NoSQL database an ideal option for semi-structured and unstructured data.
- **Scalability:** The NoSQL database is usually designed to expand horizontally by using distributed hardware clusters, rather than vertically by adding expensive and powerful servers.
- **High Performance:** The NoSQL database optimizes specific data models and access modes, achieving higher performance than attempting to perform similar functions using a relational database.
- **Powerful Functionality:** The NoSQL database provides powerful APIs and data types that are tailored to their respective data models.

## What Are the Types of NoSQL Databases?

The NoSQL database can be divided into four main types:

- **Key-Value:** The key-value database is a simpler database, with each item containing both keys and values. This is an extremely flexible NoSQL database type, as applications can fully control what is stored in the value field without any limitations. Redis and DynamoDB are two very popular key-value databases.
- **Document:** The data in the document database is stored in a document similar to the JSON (JavaScript Object Notation) object, which is very clear and intuitive. Each document contains paired fields and values. These values can typically be of various types, including strings, numbers, boolean values, arrays, or objects, and their structure is usually consistent with the object used by the developers in the code. MongoDB is a very popular document database.
- **Graph:** Graph databases aim to easily construct and run applications that are used with highly connected data sets. Typical use cases of graph databases include social networks, recommendation engines, fraud detection, and knowledge graphs. Neo4j and Giraph are very popular graph databases.
- **Wide-Column:** The wide-column storage database is well suited to the large amount of data that needs to be stored. Cassandra and HBase are both very popular wide-column storage databases.

The next picture is from [MSC Official Document] Relationship Data with NoSQL
