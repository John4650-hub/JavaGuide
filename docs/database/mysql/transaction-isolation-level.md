I don't...
Title: MySQL Service Isolation Level Details
Category: Database
Tag:

- MySQL
  I don't...

> This paper was jointly prepared by [SnailClimb](https://github.com/Snailclimb) and [guang19](https://github.com/guang19).

For a basic overview of the service, please see the introduction to the article: [MySQL Common Knowledge Point & Summary of Interviews](./mysql-Qastions-01.md#MySQL-service)

# Summary of Service Segregation Level

The SQL standard defines four levels of isolation:

- **READ-UNCOMMITTED (read uncommitted)**: Minimum level of isolation, allowing for the reading of uncommitted data to change, which may lead to dirty, phantom, or non-repeated reading.
- **READ-COMMITTED (read committed)**: Allows reading of data that has already been committed, which can prevent dirty reading, but phantom or non-repeated reading is still possible.
- **REPEATABLE-READ (repeatable read)**: Multiple readings of the same field are consistent unless the data is modified by their own business to prevent dirty and non-repeated readings, but illusions can still occur.
- **SERIALIZABLE (serialized)**: Highest level of isolation, fully subject to ACID. All matters are carried out on a case-by-case basis, so that there is no possibility of interference between them; that is to say, the level prevents dirty reading, non-repeatable reading, and phantom reading.

I don't...

Isolation level, curvature, dirty reading, serial reading.

| Level            | Description |
| :--------------- | :---------- |
| READ-UNCOMMITTED |             |
| READ-COMMITTED   |             |
| REPEATABLE-READ  |             |
| SERIALIZABLE     |             |

The default level of segregation supported by the MySQL InnoDB storage engine is **REPEATABLE-READ (repeatable read)**. `SELECT @@tx_isolation;` In MySQL 8.0, this command is changed to `SELECT @@transaction_isolation;`

```sql
MySQL> SELECT @@tx_isolation
+---------------------+
| @@tx_isolation      |
+---------------------+
| REPEATABLE-READ     |
+---------------------+
```

As can be seen from the above description of the SQL standard four levels of isolation, the standard SQL level of isolation definition does not prevent phantom reading.

But! InnoDB's realization of the REPEATABLE-READ level of isolation actually solves the problem of phantom reading, mainly in two ways:

- **Quick read**: MVCC mechanism to ensure that there is no phantom reading.
- **Current reading**: Using Next-Key Lock for locking to ensure that there is no phantom reading. Next-Key Lock is a combination of Record Lock and Gap Lock, which can only lock existing lines to avoid new lines being inserted.

The lower the level of segregation, the smaller the locking of service requests; the greater the level of segregation of most database systems is **READ-COMMITTED**, but you should know that the InnoDB storage engine is used by default **REPEATABLE-READ** without any loss of performance.

The InnoDB storage engine is generally used for SERIALIZABLE isolation level in the case of distributed services.

Chapter 7.7 of MySQL Technical Entries: InnoDB Storage Engine (Rev.2) reads:

> InnoDB storage engine provides support for XA services and supports the realization of distributed services through XA services. Distributed services are those that allow multiple separate services (transactional resources) to participate in a global transaction. Service resources are usually a relational database system, but can also be other types of resources. The global transaction requirement is either submitted or rolled back to all the services involved, which is an increase in the original ACID requirement. In addition, when using distributed services, the service isolation level of the InnoDB storage engine must be set to SERIALIZABLE.

# Practical Demonstration

I'm going to use two command lines in MySQL to simulate a multi-threaded (multi-service) reading of the same data.

The default configuration of the MySQL command line is an automatic submission, i.e., the COMMIT operation is performed as soon as the SQL statement is executed. A command is required if a transaction is to be opened visibly: `START TRANSACTION;`.

We can set the isolation level by the following command.

```sql
SET [SESSION|GLOBAL] TRANSACTION ISOLATION LEVEL [READ UNCOMMITTED|READ COMMITTED|REPEATABLE READ|SERIALIZABLE];
```

Let's take a look at some of the commands we used in the following practical exercises:

- `START TRANSACTION;` | `BEGIN;`: A transaction is opened in a prominent way.
- `COMMIT;`: Submits changes to make all changes made to the database permanent.
- `ROLLBACK;`: Rollback will close the user's transaction and reverse all
