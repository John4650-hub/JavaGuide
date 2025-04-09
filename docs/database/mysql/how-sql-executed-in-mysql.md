I don't...
title: SQL execution in MySQL
Category: Database
Tag:

- MySQL
  I don't...

> This submission is from [Carpenter](https://github.com/kinglaw1204).

This article analyzes how the next SQL statement will be executed in MySQL, including how the SQL query will flow within MySQL, and how the SQL update will be completed.

I'm going to take you through MySQL's basic structure before analysis, and I'm going to explain what MySQL is made of and what the role of these components is to help us understand and solve these problems.

# MySQL Base Structure Analysis

## 1.1 MySQL Basic Structure Overview

The figure below is a brief chart of MySQL, from which you can see very clearly how the user's SQL statement is executed within MySQL.

A brief description of the basic roles of some of the components covered in the figure below, which will be described in detail in section 1.2, helps you to understand the figure.

- **Connector:** Identification and Permission Related (on login to MySQL).
- **Query Cache:** When executing a query statement, the cache is checked (MySQL version 8.0 has removed this function because it is not practical).
- **Analyzer:** Without Cache of Impact, the SQL statement goes through the analyzer, which first checks what your SQL statement is doing, then checks your SQL statement for correctness.
- **Optimizer:** To be implemented according to the best option considered by MySQL.
- **Executor:** Executes the statement, then data is returned from the storage engine.

![MySQL Structure](https://oss.javaguide.cn/javaguide/13526879-3037b144ed09eb88.png)

In short, MySQL is divided into the Server Layer and the Storage Engine Layer:

- **Server Layer:** Mainly includes connectors, query caches, analyzers, optimizers, executors, etc., where all cross-storage engine functions are performed, such as storage processes, triggers, views, functions, etc., and a generic log module, binlog log module.
- **Storage Engine:** Mainly responsible for data storage and reading, using a plugin-type structure to support multiple storage engines such as InnoDB, MyISAM, Memory, among which the InnoDB engine has its own log module, redo log module. **The most commonly used storage engine now is InnoDB, which has been used as the default storage engine since version 5.5 of MySQL.**

### 1.2 Server Base Component Description

#### 1. Connector

Connectors are primarily associated with the functions of identification and privileges, acting as a high-level doorman.

The user is primarily responsible for access to the database and for the authentication of the user's identity, including the verification of the account password, access, etc. If the user's account password is verified, the connector will search for all the privileges of the user on the list of permissions, and then the permission logic judgment in this connection will depend on the permission data that is read at this time, i.e., the user will not be affected as long as the connection is open, even if the administrator changes the user's permissions.

#### 2. Query Cache (MySQL 8.0 version removed)

The query cache is mainly used to cache the SELECT statement we executed and the result set of that statement.

When a connection is created, when a query statement is executed, the cache is checked. MySQL verifies whether this SQL has been executed and is in memory as Key-Value. Key is a query statement, Value is the result set. If the cache is hit, the key is returned directly to the client; if it is not hit, the follow-up operation is performed, and when it is completed, the result is cached to facilitate the next call. Of course, the user's permissions are verified when the cache query is actually executed, and there are any conditions for the query.

MySQL queries do not recommend the use of caches, as bugs may be very frequent in the actual business scene, and if you update a table, all query caches on this table will be emptied. For data that is not regularly updated, the use of caches is possible.

So, in most cases, we do not recommend the use of the query cache.

MySQL 8.0 has removed the cache function, and the official view is that it is less applied in practice, so it has been simply deleted.

#### 3. Analyzer

MySQL, without a cache, enters the analyzer, which is primarily used to analyze what SQL statements are for. The analyzer is divided into several steps:

**First step, syntax analysis:** A SQL statement consists of multiple strings, first extracting keywords such as self, table for query, field name, conditions for query, etc. After these operations, we'll go to step two.

**Step two, semantic analysis:** Mainly to determine whether the SQL you entered is correct and conforms to MySQL.

After these two steps, MySQL is
