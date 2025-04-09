I don't...
Title: MySQL High Performance Optimization Standard Summary
Category: Database
Tag:

- MySQL
  I don't...

> Author: Hearings Original address: <https://www.cnblogs.com/huchong/p/10219318.html>.
>
> JavaGuide has been authorized by the author, and the original text has been refined.

# Database Naming Standard

- All database object names must be lowercase and divided by underscores.
- All database object names prohibit the use of MySQL reserved keywords (if a table name contains keyword queries, it needs to be enclosed with a single quotation).
- The name of the object in the database should be meaningful and preferably not exceed 32 characters.
- The temporary library form shall be prefixed with `tmp_` and suffixed with the date, and the backup form shall be prefixed with `bak_` and suffixed with the date (timestamp).
- All listings and column types that store the same data must be consistent (generally as associated columns, which automatically convert data in a hidden form if there is a discrepancy in the type of associated column at the time of the query, which results in the indexing of the column being invalidated and the search efficiency reduced).

# Basic Design Specifications for Databases

All tables must use the InnoDB storage engine.

In the absence of special requirements (i.e., functions that InnoDB cannot fulfill, e.g., column storage, storage of spatial data, etc.), all tables must use the InnoDB storage engine (MySQL 5.5 before defaulting to MyISAM, 5.6 after defaulting to InnoDB).

InnoDB supports services, supports line locks, and offers better recovery, higher, and better performance.

## The Character Set of the Database and the Table Uses UTF8 Together

More compatibility; the unified character set avoids ambiguity resulting from character set conversion, and the need for a different character set to be converted prior to comparison would result in the index being invalidated. The character set would require a utf8mb4 character set if there is a need in the database to store emoji expressions.

Read the article I wrote: [MySQL Character Collection](./character-set.md).

# All Tables and Fields Need to Add Comments

Add notes to tables and columns from the outset to maintain the data dictionary.

# Try to Control the Size of the Data in the Table and Suggest That It Be Contained Within 5 Million

Five million records is not a limitation of the MySQL database, and the general assembly has caused significant problems in modifying the table structure, backup, and recovery.

The size of the data can be controlled by means of historical data archiving (for log data) and sub-libraries (for operational data).

# Carefully Use MySQL Partition Table

The partition table is physically presented as multiple documents and logically as a table.

Careful selection of partition keys may make cross-division queries less efficient.

It is recommended that large data be managed in the form of a physical breakdown.

# Always Use a Column Together in a Table

Avoid more associated operations.

# Ban Preset Fields in Tables

- Naming of predefined fields is difficult to identify.
- The preset field cannot confirm the type of data stored, so it is not possible to select the appropriate type.
- The table is locked for changes to the type of preset field.

## Ban the Storage of Large Binary Data Such as Files (e.g., Pictures) in Databases

Storing documents in databases can seriously affect database performance and consume excessive storage space.

Large binary data such as documents (e.g., pictures) are usually stored on file servers, and only file address information is stored in databases.

# Don't Be Bound by the Database Paradigm

Generally, the relational database needs to be designed to meet the third normal form, but in order to meet the third normal form, we may be able to split multiple tables. When searching, there is a need to link to multiple tables, and sometimes, in order to make the query more efficient, we may lower the requirements of the normal form by retaining some redundant information in the table, also known as a denormalization. But care must be taken that the denormalization is proportionate.

# No Online Database Pressure Tests

# Ban Direct Connection to the Production Environment Database from the Development Environment and from the Test Environment

The security risks are enormous, and there is a need to be cautious of the production environment!

# Database Field Design Specifications

## Give Priority to the Smallest Data Type That Meets Storage Needs

The smaller the bytes stored, the less space is occupied, and the better the performance.

**a. Some strings can be converted to digital types for storage, for example, by converting IP addresses to integer data.**

The numbers are continuous, perform better, and occupy smaller amounts of space.

MySQL offers two ways to handle IP addresses:

- `INET_ATON()`: conversion of IP to unsigned integer (4-8 bits);
- `INET_NTOA()`: conversion of whole
