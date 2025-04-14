---
title: MySQL Index Detailed Explanation
category: Database
tag:
  - MySQL
---

> Thanks to [WT-AHA](https://github.com/WT-AHA) for improving this article, related PR: <https://github.com/Snailclimb/JavaGuide/pull/1648>.

Anyone who has gone through a few interviews should be well aware that the topic of database indexing appears with alarming frequency in interviews.

In addition to being very important for interview preparation, effectively using indexes can significantly improve SQL performance, making it a cost-effective SQL optimization technique.

## Introduction to Indexes

**An index is a data structure used for fast querying and retrieval of data, essentially functioning as a sorted data structure.**

The role of an index is similar to that of a table of contents in a book. For example, when we look up a word in a dictionary, without a table of contents, we would have to search page by page for the word we need, which is very slow; with a table of contents, we can first find the location of the word in the table and then directly turn to that page.

There are many types of underlying data structures for indexes, with common index structures including B-trees, B+ trees, Hash, and Red-Black trees. In MySQL, both InnoDB and MyISAM use B+ trees as their index structure.

## Advantages and Disadvantages of Indexes

**Advantages**:

- Using indexes can greatly speed up data retrieval (significantly reducing the amount of data scanned) and decrease the number of I/O operations, which is the primary reason for creating indexes.
- By creating unique indexes, we can ensure the uniqueness of each row of data in the database table.

**Disadvantages**:

- Creating and maintaining indexes can be time-consuming. When performing insert, delete, or update operations on data in a table, if the data has indexes, those indexes also need to be dynamically modified, which can reduce SQL execution efficiency.
- Indexes require physical file storage, which also consumes space.

However, **can using indexes always improve query performance?**

In most cases, indexed queries are faster than full table scans. However, if the amount of data in the database is not large, using indexes may not bring significant improvements.

## Selection of Underlying Data Structures for Indexes

### Hash Table

A hash table is a collection of key-value pairs, allowing for quick retrieval of the corresponding value through the key, thus enabling fast data retrieval (close to O(1)).

**Why can we quickly retrieve the value through the key?** The reason lies in the **hash algorithm**. By using a hash algorithm, we can quickly find the index corresponding to the key, and once we have the index, we can find the corresponding value.

```java
hash = hashfunc(key)
index = hash % array_size
```

![](https://oss.javaguide.cn/github/javaguide/database/mysql20210513092328171.png)

However! The hash algorithm has a **hash collision** problem, meaning that multiple different keys can end up with the same index. Typically, the common solution is the **chaining method**, which stores the colliding data in a linked list. For example, before JDK 1.8, `HashMap` used the chaining method to resolve hash collisions. However, after JDK 1.8, `HashMap` introduced Red-Black trees to improve search efficiency when the linked list becomes too long.

![](https://oss.javaguide.cn/github/javaguide/database/mysql20210513092224836.png)

To reduce the occurrence of hash collisions, a good hash function should "evenly" distribute data across the entire possible set of hash values.

The InnoDB storage engine in MySQL does not directly support conventional hash indexes. However, there is a special "Adaptive Hash Index" in the InnoDB storage engine. The Adaptive Hash Index is not a pure hash index in the traditional sense but combines the characteristics of B+ trees and hash indexes to better adapt to actual data access patterns and performance requirements. Each hash bucket in the Adaptive Hash Index is actually a small B+ tree structure. This B+ tree structure can store multiple key-value pairs, not just a single key. This helps reduce the length of hash collision chains and improves index efficiency. For a detailed introduction to the Adaptive Hash Index, you can refer to the article [MySQL Various "Buffer" and Adaptive Hash Index](https://mp.weixin.qq.com/s/ra4v1XR5pzSWc-qtGO-dBg).

Since hash tables are so fast, **why doesn't MySQL use them as the index data structure?** The main reason is that hash indexes do not support ordered and range queries. If we need to sort data in a table or perform a range query, hash indexes are not suitable. Additionally, each I/O operation can only retrieve one item.

Consider a situation:

```java
SELECT * FROM