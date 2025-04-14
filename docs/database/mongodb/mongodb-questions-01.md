---
title: Summary of Common MongoDB Interview Questions (Part 1)
category: Database
tag:
  - NoSQL
  - MongoDB
---

> A small portion of the content references descriptions from the official MongoDB documentation, which is noted here.

## MongoDB Basics

### What is MongoDB?

MongoDB is an open-source NoSQL database system based on **distributed file storage**, written in **C++**. MongoDB provides a **document-oriented** storage method, which is relatively simple and easy to operate, supports "**schema-less**" data modeling, and can store complex data types. It is a very popular **document-type database**.

Under high load, MongoDB natively supports horizontal scaling and high availability, allowing for easy addition of more nodes/instances to ensure service performance and availability. In many scenarios, MongoDB can replace traditional relational databases or key/value storage methods, providing scalable, high-availability, high-performance data storage solutions for web applications.

### What is the storage structure of MongoDB?

The storage structure of MongoDB differs from traditional relational databases and mainly consists of the following three units:

- **Document**: The most basic unit in MongoDB, composed of BSON key-value pairs, similar to rows in relational databases.
- **Collection**: A collection can contain multiple documents, similar to tables in relational databases.
- **Database**: A database can contain multiple collections, and multiple databases can be created in MongoDB, similar to databases in relational databases.

In other words, MongoDB stores data records as documents (more specifically, [BSON documents](https://www.mongodb.com/docs/manual/core/document/#std-label-bson-document-format)), which are grouped together in collections, with one or more document collections stored in a database.

**Comparison of Common Terms between SQL and MongoDB**:

| SQL            | MongoDB           |
| -------------- | ----------------- |
| Table          | Collection        |
| Row            | Document          |
| Column         | Field             |
| Primary Key    | Object ID         |
| Index          | Index             |
| Embedded Table | Embedded Document |
| Array          | Array             |

#### Document

Records in MongoDB are BSON documents, which are data structures composed of key-value pairs, similar to JSON objects, and are the basic data unit in MongoDB. The values of fields may include other documents, arrays, and arrays of documents.

![MongoDB Document](https://oss.javaguide.cn/github/javaguide/database/mongodb/crud-annotated-document..png)

The keys of documents are strings. With few exceptions, keys can use any UTF-8 character.

- Keys cannot contain `\0` (null character). This character is used to indicate the end of the key.
- `.` and `$` have special meanings and can only be used in specific contexts.
- Keys that start with an underscore `_` are reserved (not strictly enforced).

**BSON [bee·sahn]** is short for Binary [JSON](http://json.org/), which is the binary representation of JSON documents. It supports embedding documents and arrays within other documents and arrays and includes extensions that allow for the representation of data types not included in the JSON specification. For details on the BSON specification, refer to [bsonspec.org](http://bsonspec.org/), and see also [BSON Types](https://www.mongodb.com/docs/manual/reference/bson-types/).

According to Wikipedia's introduction to BJSON, BJSON has better traversal speed than JSON, which is a primary reason MongoDB chose BSON, although BJSON requires more storage space.

> Compared to JSON, BSON focuses on improving storage and scanning efficiency. Large elements in BSON documents are prefixed with length fields for easier scanning. In some cases, due to the presence of length prefixes and explicit array indexes, BSON may use more space than JSON.

![BSON Official Homepage](https://oss.javaguide.cn/github/javaguide/database/mongodb/bsonspec.org.png)

#### Collection

MongoDB collections exist within databases and have **no fixed structure**, meaning they are **schema-less**, allowing for the insertion of different formats and types of data into a collection. However, typically, the data inserted into a collection will have some degree of correlation.

![MongoDB Collection](https://oss.javaguide.cn/github/javaguide/database/mongodb/crud-annotated-collection.png)

Collections do not need to be created in advance; a new collection will be created if it does not exist when the first document is inserted or the first index is created.

Collection names can be any UTF-8 string that meets the following conditions:

- Collection names cannot be empty strings `""`.
- Collection names cannot contain `\0` (null character), which indicates the end of the collection name.
- Collection names cannot start with "system.", as this prefix is reserved for system collections. For example, the `system.users` collection stores user information for the database, and the \`system.names
