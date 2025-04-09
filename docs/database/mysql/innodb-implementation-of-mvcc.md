I don't...
Title: InnoDB storage engine for MVCC
Category: Database
Tag:

- MySQL
  I don't...

# Multi-Version Concurrency Control

MVCC is a concurrency control mechanism used to maintain consistency and segregation of data when multiple simultaneous transactions are read and written. It is achieved by maintaining multiple versions of data on each data line. When a transaction is about to modify the data in the database, MVCC creates a data snapshot for the transaction, rather than modifying the actual data line directly.

1. The reading operation (SELECT):

When a service performs a reading operation, it uses a snapshot to read. Fast reading is created on the basis of the status in the database at the time of the start of the transaction, so that services do not read changes that have not been submitted by other services. The work will be as follows:

- For the reading operation, the service finds the eligible data rows and selects the version of the data that corresponds to the time when the transaction begins to be read.
- If there is more than one version of a data line, the service selects the latest version of the data as it begins, ensuring that only the data that existed before it was read.
- Service reading is snapshot data, so changes to the data lines by other simultaneous services do not affect current service reading operations.

2. Writing operations (INSERT, UPDATE, DELETE):

When a transaction executes a writing operation, it generates a new version of the data and writes modified data into the database. The work will be as follows:

- For writing operations, a new version of the data line to be modified will be created and the modified data will be added to the new version.
- The new version of the data will have the current version number so that other services can read the corresponding version correctly.
- The data in the original version is still available for other services using snapshots, which ensures that other services are not affected by the writing of current services.

3. Presentation and rollback of services:

- When a transaction is submitted, the changes it makes will become the latest version of the database and will be visible to other services.
- When a transaction rolls back, the changes it makes will be withdrawn and the other transactions will be invisible.

4. Version recovery:

In order to prevent an infinite increase in the number of versions in the database, the MVCC will periodically recycle the versions. The recovery mechanism releases space by removing older versions of data that are no longer required.

MVCC achieves concurrency control by creating multiple versions of the data and using snapshot access. It reads a snapshot of the old version of data, writes a new version, and ensures that the original version is still available. In this way, different services can be implemented in parallel to a certain extent, without interfering with each other, thus improving the concurrency of databases and data consistency.

# Consistency does not lock and read

## Consistency does not lock reading

[**Consistent Non-Locking Reads**](https://dev.mysql.com/doc/refman/5.7/en/innodb-consistent-read.html) is usually achieved by adding a version number or time-stamping field, with a simultaneous version number +1 or with an update. When searching, compare the currently visible version number with the version number of the corresponding record, indicating that the record is visible if the version is smaller than the visible version.

In the `InnoDB` storage engine, [multi-version control](https://dev.mysql.com/doc/refman/5.7/en/innodb-multi-version.html) is the achievement of non-locking reading. If the read line is in the process of `DELETE` or `UPDATE` operation, the read operation will not wait for release from the line lock. On the contrary, the `InnoDB` storage engine is going to read a snapshot of the line, and we call it snapshot read for this way of reading historical data.

Under the two segregation levels of `Repeatable Read` and `Read Committed`, `consistent non-locking reading (MVCC)` would be used if the normal `SELECT` statement (excluding `SELECT LOCK IN SHARE MODE`, `SELECT ... FOR UPDATE`). And `MVCC` achieves re-readability and prevents partial visions under `Repeatable Read`.

# Lock Read

If the following statement is executed, it is [**Locking Reads**](https://dev.mysql.com/doc/refman/5.7/en/innodb-local-reads.html):

- `SELECT ... LOCK IN SHARE MODE`
- `SELECT ... FOR UPDATE`
- `INSERT`, `UPDATE`, `DELETE` operations

The latest version of the data, also known as `Current Read`, is read under lock. Lock reading locks records read to:

- `LOCK IN SHARE MODE`: other transactions may be blocked if the `X` lock is added to the record.
- `SELECT ... FOR UPDATE`, `INSERT`, `UPDATE`, `DELETE`: add `X` lock to the record and no lock on other transactions.

Under the non-locking of consistency,
