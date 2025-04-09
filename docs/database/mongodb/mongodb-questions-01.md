I don't...
Title: MongoDB Common Interview Summary (above)
Category: Database
Tag:

- NoSQL
- MongoDB
  I don't...

> A few references refer to the description of the MongoDB official document.

# MongoDB Foundation

What's MongoDB?

MongoDB is an open-source NoSQL database system based on **distributed file storage**, prepared with **C++**. MongoDB provides storage **for documents**, which is simpler and easier to operate, supports “**No Mode**” data modeling, can store more complex data types, and is a very popular **document-type database**.

In the case of high loads, MongoDB's natural support level is extended and available, and more nodes/examples can be easily added to ensure service performance and availability. In many scenarios, MongoDB can be used as a substitute for traditional relational databases or key/value storage, providing extended high-capacity, high-performance data storage solutions for web applications.

What's MongoDB's storage structure?

The storage structure of MongoDB differs from the traditional relational database and consists mainly of three modules:

- **Document**: The most basic unit in MongoDB consists of BSON key pairs (key-value), similar to the rows in a relational database (Row).
- **Pool**: A collection may contain multiple documents, similar to tables in a relational database (Table).
- **Database**: A database can contain multiple collections and can create multiple databases in MongoDB, similar to those in relational databases (Database).

In other words, MongoDB stores data records as documents (more specifically [BSON documents](https://www.mongodb.com/docs/manual/core/document/#std-label-bson-document-format)) where they are assembled in a collection, where one or more documents are stored in a database.

**SQL versus MongoDB common terminology**:

SQL & MongoDB
| \- - - - - - - - - - - - - - - - - - - - - - - - - - - - - |
| Table & Table |
| Row | Document |
| Col | Field |
| Primary Key | Index |
| Embedded Table | Embedded Document |
| Array |

# Document

The record in MongoDB is a BSON document, which is a data structure made up of key-value pairs, similar to the JSON object, which is the basic data unit in MongoDB. Fields may include other documents, arrays, and document arrays.

![MongoDB document](https://oss.javaguide.cn/github/javaguide/database/mongodb/crud-annotated-document.png)

A document is a string. With few exceptions, keys can use any UTF-8 characters.

- The key may not contain `0` (empty character). This character represents the end of the key.
- `.` and `$` have special significance and can only be used in a given environment.
- The following underlined `_` opening key is reserved (not strictly required).

**BSON [bee.sahn]** is the acronym for Binary [JSON](http://json.org/) and is the binary expression of the JSON document, which supports embedding documents and arrays in other documents and arrays, and contains extensions that allow the expression of data types that are not part of the JSON norm. For the content of BSON norms, see [bsonspec.org](http://bsonspec.org/) and see also [BSON type](https://www.mongodb.com/docs/manual/reference/bson-types/).

According to Wikipedia's introduction to BJSON, BJSON has a better velocity than JSON, which is also the main reason for MongoDB to choose BSON, but BJSON needs more storage space.

> Compared to JSON, BSON aims to improve storage and scanning efficiency. Large elements in BSON documents are prefixed with length fields to facilitate scanning. In some cases, BSON would use more space than JSON because of the length prefix and the presence of a visible array index.

![BSON](https://oss.javaguide.cn/github/javaguide/database/mongodb/bsonspec.org.png)

# Gather

MongoDB collections exist in databases, **without a fixed structure**, i.e. **without a mode**, which means that data in different formats and types can be inserted in the past. In general, however, there is some correlation between the insertion of data in the pool.

![MongoDB Pool](https://oss.javaguide.cn/github/javaguide/database/mongodb/crud-annotated-collection.png)

A collection does not need to be created in advance. When the first document is inserted or the first index is created, a new collection is created if the collection does not exist.

Pool names may be any UTF-8 string that:

- The collective name cannot be an empty string \`''.
- The collective name cannot contain `0` (empty character), which indicates the end of the collective name.
- The collective name cannot begin with "system." This is the prefix reserved for the system collection. For example, `system.users`, which holds user information for the database, `system.namespaces` collects information for all databases
