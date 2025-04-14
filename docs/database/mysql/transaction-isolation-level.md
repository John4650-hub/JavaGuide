---
title: Detailed Explanation of MySQL Transaction Isolation Levels
category: Database
tag:
  - MySQL
---

> This article is co-authored by [SnailClimb](https://github.com/Snailclimb) and [guang19](https://github.com/guang19).

For an overview of transactions, please refer to this article: [Summary of Common MySQL Knowledge Points & Interview Questions](./mysql-questions-01.md#MySQL-%E4%BA%8B%E5%8A%A1)

## Summary of Transaction Isolation Levels

The SQL standard defines four isolation levels:

- **READ-UNCOMMITTED**: The lowest isolation level, allowing reading of uncommitted data changes, which may lead to dirty reads, phantom reads, or non-repeatable reads.
- **READ-COMMITTED**: Allows reading of data that has been committed by concurrent transactions, preventing dirty reads, but phantom reads or non-repeatable reads may still occur.
- **REPEATABLE-READ**: Ensures that multiple reads of the same field yield consistent results unless the data is modified by the transaction itself. It prevents dirty reads and non-repeatable reads, but phantom reads may still occur.
- **SERIALIZABLE**: The highest isolation level, fully compliant with the ACID isolation level. All transactions are executed sequentially, preventing any interference between transactions, meaning this level can prevent dirty reads, non-repeatable reads, and phantom reads.

______________________________________________________________________

| Isolation Level  | Dirty Read | Non-repeatable Read | Phantom Read |
| :--------------: | :--------: | :-----------------: | :----------: |
| READ-UNCOMMITTED |     √      |          √          |      √       |
|  READ-COMMITTED  |     ×      |          √          |      √       |
| REPEATABLE-READ  |     ×      |          ×          |      √       |
|   SERIALIZABLE   |     ×      |          ×          |      ×       |

The default isolation level supported by the MySQL InnoDB storage engine is **REPEATABLE-READ**. We can check this using the command `SELECT @@tx_isolation;`, which in MySQL 8.0 has changed to `SELECT @@transaction_isolation;`

```sql
MySQL> SELECT @@tx_isolation;
+-----------------+
| @@tx_isolation  |
+-----------------+
| REPEATABLE-READ |
+-----------------+
```

From the introduction above regarding the four isolation levels defined by the SQL standard, it can be seen that the standard SQL isolation level definition does not prevent phantom reads for REPEATABLE-READ.

However! The REPEATABLE-READ isolation level implemented by InnoDB can actually resolve the issue of phantom reads, mainly in the following two situations:

- **Snapshot Read**: Ensured by the MVCC mechanism to prevent phantom reads.
- **Current Read**: Uses Next-Key Lock for locking to prevent phantom reads. Next-Key Lock is a combination of row locks (Record Lock) and gap locks (Gap Lock). Row locks can only lock existing rows, and to prevent the insertion of new rows, gap locks are relied upon.

Since lower isolation levels require fewer locks for transaction requests, most database systems use **READ-COMMITTED** as the default isolation level. However, it is important to note that the InnoDB storage engine's default use of **REPEATABLE-READ** does not incur any performance loss.

In distributed transactions, the InnoDB storage engine generally uses the SERIALIZABLE isolation level.

Chapter 7.7 of "MySQL Internals: InnoDB Storage Engine (2nd Edition)" states:

> The InnoDB storage engine provides support for XA transactions and uses XA transactions to support the implementation of distributed transactions. Distributed transactions allow multiple independent transactional resources to participate in a global transaction. Transactional resources are typically relational database systems, but can also include other types of resources. A global transaction requires that all participating transactions either commit or roll back, which raises the original ACID requirements for transactions. Additionally, when using distributed transactions, the transaction isolation level of the InnoDB storage engine must be set to SERIALIZABLE.

## Practical Demonstration

Below, I will use two MySQL command line instances to simulate the dirty read problem with multiple threads (multiple transactions) on the same data.

In the default configuration of the MySQL command line, transactions are automatically committed, meaning that after executing an SQL statement, a COMMIT operation is immediately performed. To explicitly start a transaction, the command `START TRANSACTION` is used.

We can set the isolation level using the following command:

```sql
SET [SESSION|GLOBAL] TRANSACTION ISOLATION LEVEL [READ UNCOMMITTED|READ COMMITTED|REPEATABLE READ|SERIALIZABLE]
```

Next, let's look at some concurrency control statements we will use in the practical operations below:

- `START TRANSACTION` | `BEGIN`: Explicitly starts a transaction.
- \`COMMIT
