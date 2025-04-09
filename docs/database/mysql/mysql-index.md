I don't...
title: MySQL Index Details
Category: Database
Tag:

- MySQL
  I don't...

Thanks to [WT-AHA](https://github.com/WT-AHA) for its refinement, PR: <https://github.com/Snailclimb/JavaGuide/pull/1648>.

However, it should be clear to all small partners who have experienced several interviews that the knowledge point, the database index, appears in the interviews with extraordinary frequency.

In addition to being very important for the preparation of interviews, good indexing of SQL performance improvements is evident and is a high-value SQL optimization.

# Index Description

**Index is a data structure for quick searching and retrieval of data, which in essence can be seen as a sort of sequenced data structure.**

The index function is equivalent to a catalogue of books. Take the example: when we look at the dictionary, if we don't have a catalogue, then we'll have to look for the word we need to find on one page, which is very slow; if there's a directory, we'll just have to look for the location of the word in the directory, and then just turn to that page.

There are many types of bottom-up index data structures, and common index structures are B, B+, Hash, and Red-Black. In MySQL, both InnoDB and MyISAM use B+ tree as an index structure.

# The Advantages and Disadvantages of the Index

**Advantages**

- The use of indexes can significantly speed up data retrieval (a significant reduction in the amount of data retrieved) and reduce the number of IOs, which is the most important reason for creating the index.
- The uniqueness of data in each line of the database table is guaranteed by creating a unique index.

**Disadvantages**

- The creation and maintenance of indexes take a lot of time. When data in tables are added or deleted, if there is an index, then the index also needs to be revised dynamically, which reduces the efficiency of SQL implementation.
- Indexing requires the storage of physical files and can also take some space.

However, **does the use of the index improve the search performance?**

In most cases, index queries are faster than full-table scans. However, if the amount of data in the database is small, then the use of the index may not necessarily lead to a significant increase.

# Index Bottom Data Structure Selection

# Hash Table

The Hash table is a collection of key-to-value pairs that can be quickly accessed by key to retrieve the corresponding value, allowing the Hash table to quickly retrieve data (close to O(1)).

**Why can you quickly retrieve value through key?** This is due to the **Hash algorithm** (also known as hash function). By the Hash algorithm, we can quickly find the key for the index and the index for the value.

```java
Hash = Hashfunc(key)
index = Hash %
```

(https://oss.javaguide.cn/github/javaguide/database/mysql20121051302328171.png)

But! The Hash algorithm has a problem with **Hash collisions**, which means that several different keys can end up with the same index. Usually, the solution that we use is the chaining method. The chaining method is to store the Hash collision data in a linked list. For example, before JDK 1.8, `HashMap` was the solution to the Hash collision through the chaining method. However, JDK 1.8 later introduced `HashMap`, which incorporated red-black trees to increase the efficiency of the search when the chain was too long.

(https://oss.javaguide.cn/github/javaguide/database/mysql201051309224836.png)

To reduce the occurrence of Hash collisions, a good Hash function should "evenly" spread the data across a possible Hash set.

The InnoDB storage engine of MySQL does not directly support the regular Hash index, but there is a special "Adaptive Hash Index" in the InnoDB storage engine, which is not a purely Hash index in the traditional sense, but a combination of B+Tree and Hash index features to better adapt to the data access patterns and performance needs of the actual application. Each Hash bucket that adjusts to the Hash Index is actually a small B+Tree structure. This B+Tree structure can store multiple key pairs, not just one key. This has helped to reduce the length of the Hash collision chain and has increased the efficiency of the index. For a detailed description of Adaptive Hash Index, the article can be viewed [MySQL Adaptive Hash Index of various “Buffer” types](https://mp.weixin.qq.com/s/ra4v1XR5pzSWc-qtGO-dBg).

Since the Hash table is so fast, **why is MySQL not using its indexed data structure?** Mainly because the Hash Index does not support the sequence and range of the query. If we're going to sort or range the data in the table, then the Hash index won't work. And every time IO takes only one
