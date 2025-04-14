---
title: Detailed Explanation of MySQL's Three Major Logs (binlog, redo log, and undo log)
category: Database
tag:
  - MySQL
---

> This article is contributed by the public account Programmer A Xing, and JavaGuide has made supplementary improvements.

## Introduction

MySQL logs mainly include error logs, query logs, slow query logs, transaction logs, and binary logs. Among these, the most important are the binary log (binlog), redo log, and undo log.

![](https://oss.javaguide.cn/github/javaguide/01.png)

Today, let's discuss redo log, binlog, two-phase commit, and undo log.

## redo log

The redo log is unique to the InnoDB storage engine, giving MySQL the ability to recover from crashes.

For example, if a MySQL instance crashes or goes down, upon restart, the InnoDB storage engine will use the redo log to recover data, ensuring data persistence and integrity.

![](https://oss.javaguide.cn/github/javaguide/02.png)

In MySQL, data is organized in pages. When you query a record, a page of data is loaded from the disk, and this loaded data is called a data page, which is placed into the `Buffer Pool`.

Subsequent queries first look for data in the `Buffer Pool`, and if not found, then load from the disk, reducing disk I/O overhead and improving performance.

When updating table data, the same principle applies. If the data to be updated is found in the `Buffer Pool`, it is updated directly there.

Then, the modifications made to a specific data page are recorded in the redo log buffer, which is subsequently flushed to the redo log file.

![](https://oss.javaguide.cn/github/javaguide/03.png)

> Typo alert: In step 4, the phrase "clear redo log buffe and flush to redo log" should have "buffe" corrected to "buffer".

Ideally, flushing occurs immediately upon transaction commit, but in practice, the timing of flushing is determined by policy.

> Tip: Each redo record consists of "tablespace ID + data page ID + offset + length of modified data + specific modified data".

### Flushing Timing

There are several scenarios in which InnoDB flushes the redo log to disk:

1. Transaction commit: When a transaction is committed, the redo log in the log buffer is flushed to disk (this can be controlled by the `innodb_flush_log_at_trx_commit` parameter, which will be discussed later).
1. Insufficient log buffer space: When the cached redo log in the log buffer occupies about half of its total capacity, it needs to be flushed to disk.
1. Transaction log buffer full: InnoDB uses a transaction log buffer to temporarily store redo log entries. When the buffer is full, it triggers a flush to write the logs to disk.
1. Checkpoint: InnoDB periodically performs checkpoint operations to flush dirty data (modified but not yet written to disk) to disk, along with the corresponding redo logs to ensure data consistency.
1. Background flush thread: InnoDB starts a background thread that periodically (every second) flushes dirty pages (modified but not yet written to disk) to disk, along with the related redo logs.
1. Normal server shutdown: When MySQL shuts down, the redo logs are flushed to disk.

In summary, InnoDB flushes the redo log in various situations to ensure data persistence and consistency.

We need to pay attention to setting the correct flushing policy `innodb_flush_log_at_trx_commit`. Depending on the configured flushing strategy, there may be slight data loss after a MySQL crash.

The value of `innodb_flush_log_at_trx_commit` has three options, representing three flushing strategies:

- **0**: When set to 0, it means no flushing occurs upon each transaction commit. This method has the highest performance but is the least safe, as if MySQL crashes, the last second's transactions may be lost.
- **1**: When set to 1, it means flushing occurs on every transaction commit. This method has the lowest performance but is the safest, as once a transaction is successfully committed, the redo log record is guaranteed to be on disk, with no data loss.
- **2**: When set to 2, it means that on each transaction commit, only the redo log content in the log buffer is written to the page cache (file system cache). The page cache is specifically for caching files, and the cached file here is the redo log file. This method's performance and safety are intermediate between the first two.

The default value of the flushing strategy `innodb_flush_log_at_trx_commit` is 1, which ensures no data loss. To guarantee transaction persistence, we must set it to 1.

Additionally, the InnoDB storage engine has a background thread that writes the contents of the `redo log buffer`
