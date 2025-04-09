I don't...
Title: MySQL's three logs (binlog, redo log, and undo log)
Category: Database
Tag:

- MySQL
  I don't...

> This paper is from the public-named Austrope contribution, supplemented by JavaGuide.

Foreword

MySQL logs mainly include error logs, query logs, slow query logs, transaction logs, and major categories of binary logs. Of particular importance are binlogs, redo logs, and undo logs.

(https://oss.javaguide.cn/github/javaguide/01.png)

Let's talk today about redo log, binlog, two-stage submission, and undo log.

# Redo log

Redo log is unique to the InnoDB storage engine, which gives MySQL the ability to recover from crashes.

For example, if MySQL is dead or crashes, when restarted, the InnoDB storage engine uses redo log to restore the data to ensure its persistence and integrity.

(https://oss.javaguide.cn/github/javaguide/02.png)

The data in MySQL is page-based; you search for a record, load a page of data from a hard drive, load a data page, and put it in `Buffer Pool`.

Follow-up queries are made from `Buffer Pool`, which were not hit before loading the hard drive, reducing the cost of the hard drive and improving performance.

This is also true when the table data is updated, and it is found that there is data in `Buffer Pool` to be updated, which is done directly in `Buffer Pool`.

The "what changes have been made on a data page" is then recorded in the redo log cache (`redo log buffer`) and then flushed into the redo log file.

(https://oss.javaguide.cn/github/javaguide/03.png)

> Pistol tip: Step 4 "Clean redo log buffer and flush to redo log" should be buffer.

Ideally, a flush is performed as soon as the transaction is submitted, but in practice, the timing of the flush is based on strategy.

> Petty: each redo record consists of "Table Space + Data Page + Offset + Modified Data Length + Specifically Modified Data"

# Flush Timing

InnoDB updates the log in several ways:

InnoDB writes redo logs to disks:

1. Transaction submission: When a transaction is submitted, the redo log in the log buffer will be flushed to disk (which can be controlled by `innodb_flush_log_at_trx_commit` parameters, as mentioned later).
1. Log Buffer when space is insufficient: The log buffer cached redo log already accounts for about half of the log buffer's total capacity, and these logs need to be flushed to disk.
1. Full service log buffer: InnoDB uses a service log buffer (transaction log buffer) to temporarily store service redo log entries. When the buffer zone is full, it triggers the updating of the log and the writing of the log to disk.
1. Checkpoint: InnoDB regularly carries out checkpoint operations to flush the dirty data in memory (modified but not on disk) to disk and to update the corresponding redo logs together to ensure consistency of data.
1. Background refreshing thread: InnoDB has initiated a background thread that is responsible for updating the dirty page (modified but not written to the disk data page) to disks on a cyclical basis (every 1 second) and for updating the relevant redo logs together.
1. Regularly shutting down servers: When MySQL closes, redo logs are flushed to disks.

In summary, InnoDB will flush the log in many cases to ensure the durability and consistency of the data.

We need to be careful to set the correct flush policy `innodb_flush_log_at_trx_commit`. There may be a slight data loss after a MySQL crash, depending on the flush policy of MySQL configuration.

`innodb_flush_log_at_trx_commit` has three values, i.e., three flush policies:

- **0**: Set to 0, which means that no flushing is performed on each transaction submitted. This method has the highest performance, but it is also the most insecure, because if MySQL crashes, it may lose the last one second of transactions.
- **1**: Set to 1 means that a flush is performed every time a transaction is submitted. This method has the lowest performance, but it is also the safest, because as long as the transaction is submitted successfully, the redo log records must be on disk, and no data will be lost.
- **2**: Set to 2 means that only redo log content from the log buffer is written to page cache (filesystem cache) for each transaction submitted. Page cache is a file that is used to cache files. Here, the cached file is a redo log file. The performance and safety of this approach are in between.

The default value for the flush policy `innodb_flush_log_at_trx_commit` is 1, and no data is lost when set to 1. To ensure the durability
