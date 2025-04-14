---
title: Why Redis Uses Skip Lists to Implement Sorted Sets
category: Database
tag:
  - Redis
---

## Introduction

In recent years, common data structure design questions have been frequently asked in Redis interviews. One interesting interview question is: "Why does Redis use skip lists for its sorted sets instead of balanced trees, red-black trees, or B+ trees?"

This article will use this commonly asked interview question as a starting point to provide a detailed understanding of the skip list data structure.

The overall structure of this article is illustrated in the following diagram. I will guide you from the basic usage of sorted sets to the source code analysis and implementation of skip lists, giving you a deeper understanding and mastery of the skip list used in the underlying implementation of Redis's sorted sets.

![](https://oss.javaguide.cn/javaguide/database/redis/skiplist/202401222005468.png)

## The Use of Skip Lists in Redis

First, we need to understand the usage of the skip list data structure in Redis's sorted sets. Redis has a commonly used data structure called **sorted set (zset)**. As the name suggests, it is a collection that guarantees order and uniqueness of elements, making it frequently used in scenarios like leaderboards that require statistical ranking.

Here, we will demonstrate the implementation of a leaderboard using command line. We can see that I entered three users: **xiaoming**, **xiaohong**, and **xiaowang**, with their **scores** being 60, 80, and 60, respectively, and they are finally ranked in descending order of their scores.

```bash
127.0.0.1:6379> zadd rankList 60 xiaoming
(integer) 1
127.0.0.1:6379> zadd rankList 80 xiaohong
(integer) 1
127.0.0.1:6379> zadd rankList 60 xiaowang
(integer) 1

# Return members in the specified range of the sorted set, indexed, with scores from high to low
127.0.0.1:6379> ZREVRANGE rankList 0 100 WITHSCORES
1) "xiaohong"
2) "80"
3) "xiaowang"
4) "60"
5) "xiaoming"
6) "60"
```

At this point, we can check the data structure of the zset using the `object` command, and we can see that the current sorted set is still stored as a **ziplist**.

```bash
127.0.0.1:6379> object encoding rankList
"ziplist"
```

The designer considered that Redis stores data in memory, and to save precious memory space, when the number of elements in the sorted set is less than 128 and each element is less than 64 bytes, it will use a ziplist. The default threshold for this setting comes from the following two configuration items.

```bash
zset-max-ziplist-value 64
zset-max-ziplist-entries 128
```

Once an element in the sorted set exceeds one of these two thresholds, it will switch to a **skiplist** (actually a dict + skiplist, which also uses a dictionary to improve the efficiency of retrieving specific elements).

Let's add an element greater than 64 bytes and see that the underlying storage of the sorted set switches to a skiplist.

```bash
127.0.0.1:6379> zadd rankList 90 yigemingzihuichaoguo64zijiedeyonghumingchengyongyuceshitiaobiaodeshijiyunyong
(integer) 1

# Exceeds the threshold, switches to skiplist
127.0.0.1:6379> object encoding rankList
"skiplist"
```

In other words, ZSet has two different implementations: ziplist and skiplist. The specific rules for which structure to use for storage are as follows:

- Use ziplist when the sorted set object meets both of the following conditions:
  1. The number of key-value pairs in ZSet is less than 128.
  1. The length of each element is less than 64 bytes.
- If the above two conditions are not met, then use skiplist.

## Implementing a Skip List

To better answer the above question and to understand and master skip lists, we can help readers understand this data structure by implementing a simple skip list.

We all know that the average time complexity for adding, querying, and deleting in a sorted linked list is **O(n)**, which grows linearly. Therefore, once the number of nodes reaches a certain size, its performance will be very poor. A skip list can be understood as building multiple levels of indexes on top of the original linked list, allowing for a time complexity of **O(log n)** for search, insert
