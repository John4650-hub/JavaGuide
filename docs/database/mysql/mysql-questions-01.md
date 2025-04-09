I don't...
Title: MySQL Common Interview Summary\
Category: Database\
Tag:

- MySQL
- Big factory interview\
  Head:
- Meta
- Name: keywords\
  Content: MySQL Foundation, MySQL Foundation, MySQL Storage Engine, MySQL query cache, MySQL Service, MySQL Lock, etc.
- Meta
- Name: description\
  Content: An article summarizing knowledge points and interviews that are common to MySQL, covering MySQL Foundation, MySQL Foundation, MySQL Storage Engine, MySQL Query Cache, MySQL Service, MySQL Lock.\
  I don't...

@include: @small-advertition.snippet.md-->

# MySQL Foundation

What's a relational database?

By definition, the Relational Database (RDB) is a relationship-based database. The relational model shows the link between the data stored in the database (one-to-one, one-to-many, many-to-many).

In the relational database, our data is stored in various tables (e.g., user tables) and each row of the table contains data (e.g., information on a user).

[Relational database table relationship](https://oss.javaguide.cn/java-guide-blog/5e3c1a71724a38245a43b02_99bf70d46cc247be878de3a88f0c44.png)

Most relational databases use SQL to operate data in the database. Furthermore, most relational databases support the four main features of the service (ACID).

**What are the common relational databases?**

MySQL, PostgreSQL, Oracle, SQL Server, SQLite (the local storage of chat records is SQLite)...

What's SQL?

SQL is a structured query language (Structured Query Language) designed to deal with databases and to provide a simple and efficient way of reading and writing data from the databases.

Almost all mainstream relational databases support SQL and are highly applicable. Moreover, some non-relational databases are compatible with SQL or use a query language similar to SQL.

SQL can help us:

- Create new databases, data tables, fields;
- Add, delete, modify, and query data in the database;
- Create new views, functions, stored procedures;
- Perform simple data analysis of data in databases;
- Use Hive, Spark SQL for big data;
- Match SQLFlow with machine learning;
- ...

What is MySQL?

![MySQL](https://oss.javaguide.cn/github/javaguide/csdn/20210327143351823.png)

**MySQL is a relational database that is mainly used to store some of the data in our system, such as user information.**

Because MySQL is an open-source, free, and more mature database, it is used extensively in various systems. Anyone can download it with permission from the GPL (General Public License) and modify it according to their needs for personalization. The default port number for MySQL is **3306**.

# MySQL, what good is it?

The question is essentially why MySQL is so popular.

MySQL has the following main advantages:

1. Stable and functionally mature.
1. Open source and free of charge.
1. There is a wealth of documentation, both detailed official documents and very high-quality articles for study.
1. Open boxes, simple operations, and low maintenance costs.
1. Compatibility, supporting common operating systems and the development of multiple languages.
1. Active communities and ecological soundness.
1. Excellent service support, and the InnoDB storage engine is defaulted to use REPEATABLE-READ without any loss of performance, and the InnoDB implementation of the REPEATABLE-READ isolation level can actually solve the problem of phantom reads.
1. Support for split library tables, reading and writing separation, and high availability.

# MySQL field types

MySQL field types can be easily divided into three broad categories:

- **Numerical Type**: Integer (TINYINT, SMALLINT, INT, and BIGINT), Float (FLOAT and DOUBLE), Decimal (DECIMAL)
- **String Type**: CHAR, VARCHAR, TINYTEXT, TEXT, MEDIUMTEXT, LONGTEXT, TINYBLOB, BLOB, MEDIUMBLOB, and LONGBLOB, most commonly CHAR and VARCHAR.
- **Date/Time Type**: YEAR, TIME, DATE, DATETIME, and TIMESTAMP, etc.

I didn't draw the next picture. I forgot where it was kept. It was a good summary.

![MySQL Common Field Type Summary](https://oss.javaguide.cn/github/javaguide/mysql/summary-of-mysql-field-types.png)

MySQL field types are numerous, and I will select some of the field types that are frequently developed and frequently interviewed for detailed presentation in the form of interviews. InnoDB storage engine, without special description.

In addition, it is recommended to read Chapter IV of High Performance MySQL (third edition) for a detailed description of MySQL field type optimization.

# Integer type UNSIGNED properties?

The integer type in
