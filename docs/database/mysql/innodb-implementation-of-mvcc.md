---
title: Implementation of MVCC in InnoDB Storage Engine
category: Database
tag:
  - MySQL
---

## Multi-Version Concurrency Control (MVCC)

MVCC is a concurrency control mechanism used to maintain data consistency and isolation when multiple concurrent transactions read and write to the database simultaneously. It achieves this by maintaining multiple versions of data for each data row. When a transaction needs to modify data in the database, MVCC creates a data snapshot for that transaction instead of directly modifying the actual data row.

1. Read Operations (SELECT):

When a transaction performs a read operation, it uses snapshot reading. Snapshot reading is based on the state of the database at the time the transaction started, so the transaction does not read modifications made by other transactions that have not yet been committed. The specific workings are as follows:

- For read operations, the transaction looks for data rows that meet the criteria and selects the version of the data that corresponds to its transaction start time for reading.
- If a data row has multiple versions, the transaction selects the latest version that is not later than its start time, ensuring that the transaction only reads data that existed before it started.
- The transaction reads snapshot data, so modifications made by other concurrent transactions to the data row do not affect the current transaction's read operation.

2. Write Operations (INSERT, UPDATE, DELETE):

When a transaction performs a write operation, it generates a new data version and writes the modified data to the database. The specific workings are as follows:

- For write operations, the transaction creates a new version for the data row to be modified and writes the modified data into the new version.
- The new version of the data carries the version number of the current transaction so that other transactions can correctly read the corresponding version of the data.
- The original version of the data still exists for other transactions to use snapshot reading, ensuring that other transactions are not affected by the write operations of the current transaction.

3. Transaction Commit and Rollback:

- When a transaction commits, the modifications it made become the latest version of the database and are visible to other transactions.
- When a transaction rolls back, the modifications it made are undone and are not visible to other transactions.

4. Version Cleanup:

To prevent the versions in the database from growing indefinitely, MVCC periodically performs version cleanup. The cleanup mechanism deletes old version data that is no longer needed, thereby freeing up space.

MVCC achieves concurrency control by creating multiple versions of data and using snapshot reading. Read operations use snapshots of old version data, write operations create new versions, and ensure that the original versions remain available. This allows different transactions to execute concurrently to a certain extent without interfering with each other, thereby improving the concurrency performance and data consistency of the database.

## Consistent Nonlocking Reads and Locking Reads

### Consistent Nonlocking Reads

For [**Consistent Nonlocking Reads**](https://dev.mysql.com/doc/refman/5.7/en/innodb-consistent-read.html), the typical approach is to add a version number or timestamp field, incrementing the version number by 1 or updating the timestamp when data is modified. During queries, the current visible version number is compared with the corresponding record's version number; if the record's version is less than the visible version, it indicates that the record is visible.

In the `InnoDB` storage engine, [multi-versioning](https://dev.mysql.com/doc/refman/5.7/en/innodb-multi-versioning.html) is the implementation of nonlocking reads. If the row being read is undergoing a `DELETE` or `UPDATE` operation, the read operation does not wait for the row lock to be released. Instead, the `InnoDB` storage engine reads a snapshot of the row's data; this method of reading historical data is called snapshot read.

In the `Repeatable Read` and `Read Committed` isolation levels, if a normal `SELECT` statement is executed (excluding `SELECT ... LOCK IN SHARE MODE`, `SELECT ... FOR UPDATE`), it will use `Consistent Nonlocking Reads (MVCC)`. Additionally, in `Repeatable Read`, `MVCC` implements repeatable reads and prevents phantom reads.

### Locking Reads

If the following statements are executed, it is a [**Locking Read**](https://dev.mysql.com/doc/refman/5.7/en/innodb-locking-reads.html):

- `SELECT ... LOCK IN SHARE MODE`
- `SELECT ... FOR UPDATE`
- `INSERT`, `UPDATE`, `DELETE` operations

In a locking read, the latest version of the data is read, which is also referred to as `Current Read`. Locking reads will lock the records that are read:

- `SELECT ... LOCK IN SHARE MODE`: applies an `S` lock to the record, allowing other transactions to also apply `S` locks, but if an `X` lock is applied, it will be blocked.
- `SELECT ... FOR UPDATE`, `INSERT`, `UPDATE`, \`
