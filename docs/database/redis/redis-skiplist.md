I don't...
Title: Why does Redis use a jump schedule to achieve an orderly assembly?
Category: Database
Tag:

- Redis
  I don't...

Foreword

In recent years, Redis interviews have involved a lower-level design of common data structures, one of which is a more interesting question: “Why do the orderly bottom of the Redis collections use jump tables instead of balancing trees, red-black trees, or B+ trees?”.

This paper is based on the interview, frequently asked by this major factory, entitled "Entry point", which gives you a detailed idea of the structure of the jump table.

The overall context of this paper is as shown in the graph below, where the readers will go from the basic use of the sequenced assembly to the source code analysis and realization of the jump table, giving you a deeper understanding of what is achieved at the bottom of the sequenced assembly of Redis.

![Jump Table](https://oss.javaguide.cn/javaguide/database/redis/skiplist/202402005468.png)

# Use of the jump table in Redis

Here we need to know about the use of an orderly data setup used by Redis to jump-table, which has a more common data set called **an orderly set (sorted set, zset)**. Just as it is the only set of elements that can ensure order, it is often used in a statistical setting such as rankings.

Here we can demonstrate the achievement of the rankings in the form of a command line, where three users are entered: **xiaoming**, **xiaohong**, **xiaowang**, with **scores** of 60, 80, and 60, respectively, and are eventually ranked according to the order of downscaling.

```bash
127.0.0.1:6379> zadd rankList 60 xiaoming
(integer) 1
127.0.0.1:6379> zadd rankList 80 xiaohong
(integer) 1
127.0.0.1:6379> zadd rankList 60 xiaowang
(integer) 1

# Return the members in an orderly concentration specified, indexed, scores from high to low
127.0.0.1:6379> ZREVRANGE rankList 0 100 WITHSCORES
1) "xiaohong"
2) "80"
3) "xiaowang"
4) "60"
5) "xiaoming"
6) "60"
```

At this point, we look at the data structure of zset through the `OBJECT` command and see whether **ziplist (compressed list)** is currently stored in an orderly collection.

```bash
127.0.0.1:6379> OBJECT rankList
"ziplist"
```

Because the designer uses a ziplist when the sequenced collection of elements is less than 64 bytes and the number is less than 128, considering that the Redis data is stored in memory, in order to save valuable memory space, the default value for this threshold is set from the two configurations below.

```bash
zset-max-ziplist-value 64
zset-max-ziplist-entries 128
```

Once an element in an orderly assembly exceeds one of these two thresholds, it is converted to **skiplist** (actually dict+skiplist, and dictionaries are used to increase the efficiency of obtaining specified elements).

We might want to add an element greater than 64 bytes to see an orderly assembly of bottom storage to skiplist.

```bash
127.0.0.1:6379> zadd rankList 90 yigemingzihuichao 64zieduiedonghuminghenggyuceshiaochiunyong
(integer) 1

# Over the threshold, turn to the jump table
127.0.0.1:6379> OBJECT rankList
"skiplist"
```

In other words, ZSet has two different realizations, i.e., ziplist and skiplist, with the following rules for the specific structure used for storage:

- ziplist:
  1. ZSet saves less than 128 key values;
  1. The length of each element is less than 64 bytes.
- If the above two conditions are not met, then use skiplist.

# Handwriting a jump table

In order to better answer the above questions and to better understand and master the jump table, it is possible to help readers understand this data structure by hand-writing a simple jump table.

We all know that the average time when an orderly chain is added, checked, and deleted is **O(n)**, i.e., linear growth, so once the number of nodes reaches a certain volume, their performance is very poor. The jump table is perfectly understandable as a multi-level index based on the original chain, where the time complexity of adding or deleting the search is changed to **O(log n)**.

There may be some abstra
