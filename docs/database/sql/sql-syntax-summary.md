---
title: Summary of Basic SQL Syntax
category: Database
tag:
  - Database Basics
  - SQL
---

> This article is organized and improved based on the following two resources:
>
> - [SQL Syntax Quick Reference](https://juejin.cn/post/6844903790571700231)
> - [Comprehensive MySQL Tutorial](https://www.begtut.com/mysql/mysql-tutorial.html)

## Basic Concepts

### Database Terminology

- `Database` - A container for storing organized data (usually a file or a set of files).
- `Table` - A structured list of a specific type of data.
- `Schema` - Information about the layout and characteristics of the database and tables. The schema defines how data is stored in tables, what types of data are stored, how data is decomposed, and how parts of the information are named. Both databases and tables have schemas.
- `Column` - A field in a table. All tables consist of one or more columns.
- `Row` - A record in a table.
- `Primary Key` - A column (or a set of columns) whose values uniquely identify each row in the table.

### SQL Syntax

SQL (Structured Query Language) is managed by the ANSI standards committee, hence it is referred to as ANSI SQL. Each DBMS has its own implementation, such as PL/SQL, Transact-SQL, etc.

#### SQL Syntax Structure

![](https://oss.javaguide.cn/p3-juejin/cb684d4c75fc430e92aaee226069c7da~tplv-k3u1fbpfcp-zoom-1.png)

The SQL syntax structure includes:

- **`Clause`** - Components of statements and queries. (In some cases, these are optional.)
- **`Expression`** - Can produce any scalar value or be derived from columns and rows of a database table.
- **`Predicate`** - Specifies conditions for evaluating SQL three-valued logic (3VL) (true/false/unknown) or Boolean truth values, and limits the effects of statements and queries or alters program flow.
- **`Query`** - Retrieves data based on specific conditions. This is an important component of SQL.
- **`Statement`** - Can permanently affect schemas and data, and can also control database transactions, program flow, connections, sessions, or diagnostics.

#### Key Points of SQL Syntax

- **SQL statements are case-insensitive**, but whether database table names, column names, and values are case-sensitive depends on the specific DBMS and configuration. For example, `SELECT`, `select`, and `Select` are the same.
- **Multiple SQL statements must be separated by a semicolon (`;`).**
- When processing SQL statements, **all whitespace is ignored**.

SQL statements can be written in a single line or split into multiple lines.

```sql
-- Single line SQL statement

UPDATE user SET username='robot', password='robot' WHERE username = 'root';

-- Multi-line SQL statement
UPDATE user
SET username='robot', password='robot'
WHERE username = 'root';
```

SQL supports three types of comments:

```sql
## Comment 1
-- Comment 2
/* Comment 3 */
```

### SQL Classification

#### Data Definition Language (DDL)

Data Definition Language (DDL) is the part of SQL responsible for defining data structures and database objects.

The main function of DDL is to **define database objects**.

The core commands of DDL are `CREATE`, `ALTER`, `DROP`.

#### Data Manipulation Language (DML)

Data Manipulation Language (DML) is used for database operations, executing access operations on database objects and data.

The main function of DML is to **access data**, so its syntax is primarily focused on **reading and writing databases**.

The core commands of DML are `INSERT`, `UPDATE`, `DELETE`, `SELECT`. These four commands are collectively referred to as CRUD (Create, Read, Update, Delete).

#### Transaction Control Language (TCL)

Transaction Control Language (TCL) is used to **manage transactions in the database**. These manage changes made by DML statements. It also allows grouping statements into logical transactions.

The core commands of TCL are `COMMIT`, `ROLLBACK`.

#### Data Control Language (DCL)

Data Control Language (DCL) is a set of commands that control data access rights, allowing control over specific user accounts regarding database objects such as tables, views, stored procedures, and user-defined functions.

The core commands of DCL are `GRANT`, `REVOKE`.

DCL primarily focuses on **controlling user access rights**, so its commands are not complex. The permissions that can be controlled by DCL include: `CONNECT`, `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `EXECUTE`, `USAGE`, `REFERENCES`.

The supported permission controls may vary depending on different DBMS and security entities.
