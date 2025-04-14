---
title: MySQL High Performance Optimization Specification Summary
category: Database
tag:
  - MySQL
---

> Author: Listening to the Wind Original Article: <https://www.cnblogs.com/huchong/p/10219318.html>。
>
> JavaGuide has obtained authorization from the author and has made improvements and supplements to the original content.

## Database Naming Conventions

- All database object names must use lowercase letters and be separated by underscores.
- The use of MySQL reserved keywords in database object names is prohibited (if a keyword is included in the table name, it must be enclosed in single quotes during queries).
- The naming of database objects should be meaningful and should ideally not exceed 32 characters.
- Temporary tables must have a prefix of `tmp_` and a suffix of the date, while backup tables must have a prefix of `bak_` and a suffix of the date (timestamp).
- All columns that store the same data must have consistent names and data types (generally as associated columns; if the data types of associated columns are inconsistent during queries, implicit type conversion will occur, which can invalidate indexes on the columns and reduce query efficiency).

## Basic Database Design Specifications

### All tables must use the InnoDB storage engine

Unless there are special requirements (i.e., functionalities that InnoDB cannot meet, such as column storage, storage space data, etc.), all tables must use the InnoDB storage engine (MySQL 5.5 and earlier defaulted to MyISAM, while 5.6 and later default to InnoDB).

InnoDB supports transactions, row-level locking, better recovery, and performs better under high concurrency.

### The character set for databases and tables must uniformly use UTF8

Better compatibility; a unified character set can avoid garbled characters caused by character set conversion. Comparing different character sets requires conversion, which can invalidate indexes. If the database needs to store emoji characters, the character set should use utf8mb4.

I recommend reading this article I wrote: [Detailed Explanation of MySQL Character Sets](../character-set.md).

### All tables and fields must have comments added

Use the comment clause to add remarks to tables and columns, maintaining a data dictionary from the start.

### Try to control the size of data in a single table, recommended to keep it under 5 million

5 million is not a limitation of MySQL; larger sizes can cause significant issues with modifying table structures, backups, and recovery.

You can control data size using historical data archiving (applicable to log data), sharding (applicable to business data), etc.

### Use MySQL partitioned tables with caution

Partitioned tables physically appear as multiple files but logically appear as a single table.

Carefully choose partition keys, as cross-partition queries may have lower efficiency.

It is recommended to manage large data using physical sharding.

### Frequently used columns should be placed in the same table

Avoid more join operations.

### Prohibit the establishment of reserved fields in tables

- The naming of reserved fields is difficult to make meaningful.
- The data type of reserved fields cannot be confirmed, making it impossible to choose an appropriate type.
- Modifying the type of reserved fields will lock the table.

### Prohibit storing large binary data (such as images) in the database

Storing files in the database can severely impact performance and consume excessive storage space.

Large binary data (such as images) is typically stored on file servers, with the database only storing file address information.

### Do not be constrained by database normalization

Generally, when designing a relational database, it is necessary to meet the third normal form. However, to satisfy the third normal form, we may split into multiple tables. During queries, multiple tables need to be joined, and sometimes to improve query efficiency, we may relax normalization requirements and store some redundant information in tables, which is also known as denormalization. However, it is important to ensure that denormalization is done in moderation.

### Prohibit conducting database stress tests online

### Prohibit direct connections to the production database from development or testing environments

This poses significant security risks; one must have a sense of reverence for the production environment!

## Database Field Design Specifications

### Prioritize selecting the smallest data type that meets storage needs

The smaller the storage bytes, the less space it occupies, and the better the performance.

**a. Some strings can be converted to numeric types for storage, such as converting IP addresses to integer data.**

Numbers are continuous, perform better, and occupy less space.

MySQL provides two methods to handle IP addresses:

- `INET_ATON()`: Converts IP to an unsigned integer (4-8 bits);
- `INET_NTOA()`: Converts an integer IP back to an address.

Before inserting data, use `INET_ATON()` to convert the IP address to an integer; when displaying data, use `INET_NTOA()` to convert the integer IP address back to an address for display.

\*\*b. For
