---
title: The Execution Process of SQL Statements in MySQL
category: Database
tag:
  - MySQL
---

> This article is contributed by [木木匠](https://github.com/kinglaw1204).

This article will analyze the execution process of an SQL statement in MySQL, including how SQL queries flow internally in MySQL and how SQL statement updates are completed.

Before the analysis, I will first guide you through the basic architecture of MySQL. Understanding what components make up MySQL and what roles these components play can help us comprehend and solve these issues.

## I. Analysis of MySQL Architecture

### 1.1 Overview of MySQL Architecture

The diagram below provides a brief overview of the architecture of MySQL, from which you can clearly see how user SQL statements are executed internally in MySQL.

Let's briefly introduce some of the components involved in the diagram to help everyone understand it. Section 1.2 will provide a detailed explanation of these components' roles.

- **Connector:** Related to authentication and permissions (for logging into MySQL).
- **Query Cache:** When executing a query statement, it first checks the cache (removed in MySQL 8.0 as this feature was not very practical).
- **Parser:** If the cache is not hit, the SQL statement goes through the parser, which means it first checks what your SQL statement is intended to do and then checks whether the SQL syntax is correct.
- **Optimizer:** Executes according to what MySQL deems the optimal plan.
- **Executor:** Executes the statement and then fetches the data from the storage engine.

![](https://oss.javaguide.cn/javaguide/13526879-3037b144ed09eb88.png)

In simple terms, MySQL is mainly divided into two layers: Server layer and storage engine layer.

- **Server Layer:** Mainly includes the connector, query cache, parser, optimizer, executor, etc. All cross-storage-engine functionalities are implemented at this layer, such as stored procedures, triggers, views, functions, etc., along with a general logging module, the binlog logging module.
- **Storage Engine:** Mainly responsible for data storage and retrieval, using a replaceable plugin architecture that supports multiple storage engines like InnoDB, MyISAM, Memory, etc. Among these, the InnoDB engine has its own logging module, the redolog module. **The most commonly used storage engine now is InnoDB, which has been the default storage engine since MySQL 5.5.**

### 1.2 Introduction of Basic Components in the Server Layer

#### 1) Connector

The connector is mainly related to functions associated with authentication and permissions, much like a high-level gatekeeper.

It is primarily responsible for user login to the database, performing user identity verification, including account password validation, permissions, etc. If the user's account password is validated, the connector will query the user's permissions from the permissions table. Subsequently, permission logic judgments in this connection will depend on the permissions data read at that time. This means that as long as the connection remains open, even if the administrator modifies the user's permissions, the user will not be affected.

#### 2) Query Cache (removed in MySQL 8.0)

The query cache is mainly used to cache the SELECT statements we execute along with their result sets.

After establishing a connection, when executing a query statement, the system first checks the cache. MySQL will verify whether this SQL has been executed before and caches it in memory in a Key-Value format, where Key is the query statement and Value is the result set. If the cache key is hit, the result will be returned directly to the client; if not, it will execute the following operations and cache the results for the next call. However, during the actual execution of the cached query, user permissions will still be checked to verify whether the query conditions on the table are valid.

Using caching for MySQL queries is generally not recommended because query cache invalidation can happen very frequently in real-world business scenarios. For instance, if you update a table, all cached queries on that table will be cleared. For data that does not change frequently, using caching may still be beneficial.

Therefore, in most situations, we do not recommend using query caching.

The query cache feature was removed in MySQL 8.0 since the official opinion was that its application scenarios were quite limited.

#### 3) Parser

If MySQL does not hit the cache, it will proceed to the parser. The parser is mainly used to analyze what the SQL statement is intended to do and is divided into several steps:

**Step 1: Lexical Analysis.** An SQL statement consists of multiple strings, and the first step is to extract keywords, such as SELECT, identify the queried table, fields, and query conditions, etc. After completing these operations, it will proceed to the second step.

**Step 2: Syntax Analysis.** This mainly determines whether the entered SQL is correct and conforms to MySQL's syntax.

After completing these two steps, MySQL prepares to execute the statement, but how to execute it optimally is where the optimizer comes in.

#### 4) Optimizer

The optimizer's role is to execute according to what it considers the optimal execution plan (sometimes it may not be the best; this article delves into this knowledge). For example, choices regarding indexing when multiple indexes exist or determining the join order when multiple tables are queried.

After passing through the optimizer, the specifics of how the statement will be executed are determined.

#### 5) Executor

Once the execution plan is selected, MySQL is ready to start executing. Before execution, it will check whether the user has the necessary permissions. If permission is lacking, an error message will be returned; if the permission exists, it will call the engine's interface to return the execution result.

## II. Statement Analysis

### 2.1 Query Statements

After discussing the above, how exactly is an SQL statement executed? Our SQL can be divided into two types: queries and updates (addition, modification, deletion). Let’s analyze the execution of a query statement, as follows:

```sql
SELECT * FROM tb_student A WHERE A.age = '18' AND A.name = '张三';
```

Combining the previous explanation, let’s analyze the execution flow of this statement:

- First, check if the statement has permission. If not, return an error message immediately; if there is permission, in MySQL 8.0 and earlier versions, it will first check the query cache using this SQL statement as the key to see if there is a result in memory. If found, it will return the cache; if not, it will execute the next step.

- The parser will perform lexical analysis, extracting the critical elements of the SQL statement, such as determining that this statement involves querying (SELECT), identifying the table name is tb_student, querying all columns, and establishing that the query condition is id = '1'. Then, it checks for potential syntax errors, for instance, whether the keywords are correct, etc. If everything is fine, it proceeds to the next step.

- Next, the optimizer will determine the execution plan. The above SQL statement can have two execution plans: a. First, query students with the name "张三," and then check if their age is 18. b. First, find students aged 18, then query for those named "张三." The optimizer will choose the most efficient plan based on its optimization algorithms (what it considers best may not always be the best). Once the execution plan is confirmed, it is ready to execute.

- Conduct permission checks; if not permitted, return an error message. If permission is granted, it will call the database engine interface and return the execution results from the engine.

### 2.2 Update Statements

Having covered the execution process for a query SQL, let’s now see how an update statement is executed. The SQL statement is as follows:

```sql
UPDATE tb_student A SET A.age = '19' WHERE A.name = '张三';
```

We are updating the age for 张三 (Zhang San). In actual databases, you definitely wouldn't set the age field like this, or the technical lead would get mad. This statement will basically follow the execution process referenced in the previous query, but while executing the update, logging is required, which introduces the logging module. MySQL's built-in logging module is **binlog (binary log)**, which can be used by all storage engines. The commonly used InnoDB engine also has its own logging module, the **redo log**. Let’s explore the execution process of this statement using the InnoDB mode. The process is as follows:

- First, locate the data for 张三; the query cache will not be used because the update statement will invalidate any associated query caches.
- Next, retrieve the query statement, change age to 19, call the engine API interface, and write this row of data. The InnoDB engine stores the data in memory while also recording a redo log. At this point, the redo log enters the prepare state, then informs the executor that the execution is complete and can be submitted at any time.
- Upon receiving the notification, the executor records the binlog and then calls the engine interface to change the redo log to the committed state.
- The update is complete.

**Some may wonder, why use two logging modules—can't just one suffice?**

This is because MySQL initially did not have the InnoDB engine (which was introduced as a plugin by another company). The default engine was MyISAM. However, since we know the redo log is unique to the InnoDB engine and other storage engines do not possess it, this results in a lack of crash-safe capability (crash-safe capability ensures that even if the database has an unexpected restart, previously committed records will not be lost). The binlog is only used for archiving.

Using just one logging module is not prohibited; it's just that the InnoDB engine relies on the redo log to support transactions. Then, some may ask, while using two logging modules, wouldn’t that be overly complex? Why does the redo log introduce a prepare pre-commit state? Let's explain why this is necessary using a proof by contradiction:

- **If we write the redo log first and commit, then write the binlog:** Suppose we finish writing the redo log, the machine crashes, and the binlog is not written. After rebooting, the machine will recover data through the redo log, but it won’t have a record in binlog, leading to data loss during subsequent backups and problems with master-slave synchronization.
- **If we write the binlog first and then the redo log:** Suppose we finish writing the binlog, and then the machine restarts abnormally. Since there is no redo log, this machine cannot recover that record, but the binlog has a record of it, leading to inconsistencies in data.

If we adopt a two-phase commit mechanism for the redo log, it would prevent the aforementioned issues by writing the binlog first and then committing the redo log, thus ensuring data consistency. Now the question arises: is there an extreme case? Suppose the redo log is in a prepare state, and the binlog has already been written—what happens if an unexpected restart occurs?

This depends on MySQL's handling mechanism, which is as follows:

- Determine if the redo log is complete; if it is complete, it will commit immediately.
- If the redo log is only in the prepare state but not committed, it will verify the completeness of the binlog. If the binlog is complete, it commits the redo log; if not, it rolls back the transaction.

This approach solves the issue of data consistency.

## III. Summary

- MySQL is primarily divided into the Server layer and the engine layer. The Server layer mainly includes the connector, query cache, parser, optimizer, executor, along with a logging module (binlog), which can be shared by all execution engines, and the redolog, which is exclusive to InnoDB.
- The engine layer is plugin-based, currently including MyISAM, InnoDB, Memory, etc.
- The execution flow of a query statement is as follows: permission check (if cache hit) ---> query cache ---> parser ---> optimizer ---> permission check ---> executor ---> engine
- The execution flow of an update statement is as follows: parser ---> permission check ---> executor ---> engine ---> redo log (prepare state) ---> binlog ---> redo log (commit state)

## IV. References

- "MySQL Practical Experience: 45 Lessons"
- MySQL 5.6 Reference Manual: <https://dev.MySQL.com/doc/refman/5.6/en/>

<!-- @include: @article-footer.snippet.md -->
