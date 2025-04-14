---
title: Detailed Explanation of 3 Common Cache Read/Write Strategies
category: Database
tag:
  - Redis
---

I've seen many friends list "**proficient in using cache**" on their resumes, but when I ask them about the "**3 common read/write strategies for cache**," they often look confused.

In my opinion, this issue arises because when we learn Redis, we might only write some simple demos without paying attention to cache read/write strategies, or we might not even be aware of them.

However, understanding the 3 common cache read/write strategies is very helpful for using cache in real work and for interview questions about cache!

**The three patterns introduced below each have their pros and cons; there is no best pattern. Choose a cache read/write pattern that suits your specific business scenario.**

### Cache Aside Pattern

**The Cache Aside Pattern is a commonly used cache read/write pattern, particularly suitable for scenarios with a high volume of read requests.**

In the Cache Aside Pattern, the server needs to maintain both the database (db) and the cache, with the db results being the authoritative source.

Let's take a look at the steps for reading and writing cache under this strategy.

**Write:**

- First, update the db.
- Then, delete the cache directly.

Here’s a simple diagram to help you understand the writing steps.

![](https://oss.javaguide.cn/github/javaguide/database/redis/cache-aside-write.png)

**Read:**

- Read data from the cache; if found, return it directly.
- If not found in the cache, read the data from the db and return it.
- Then, place the data into the cache.

Here’s a simple diagram to help you understand the reading steps.

![](https://oss.javaguide.cn/github/javaguide/database/redis/cache-aside-read.png)

If you only understand the above content, it is far from enough; we also need to grasp the underlying principles.

For example, the interviewer might follow up with: "**During the data writing process, can we delete the cache first and then update the db?**"

**Answer:** Absolutely not! Because this could lead to the problem of **inconsistency between the database (db) and the cache.**

For instance: If request 1 writes data A first, and request 2 subsequently reads data A, it is very likely to cause data inconsistency.

This process can be simply described as:

> Request 1 deletes data A from the cache -> Request 2 reads data from the db -> Request 1 updates data A in the db.

After you answer this, the interviewer might immediately ask: "**During the data writing process, is it okay to update the db first and then delete the cache?**"

**Answer:** Theoretically, there could still be a problem with data inconsistency, but the probability is very low because the write speed of the cache is much faster than that of the database.

For example: If request 1 reads data A first, and request 2 subsequently writes data A, and data A is not in the cache before request 1, there could still be a problem with data inconsistency.

This process can be simply described as:

> Request 1 reads data A from the db -> Request 2 updates data A in the db (at this point, there is no data A in the cache, so no cache deletion is needed) -> Request 1 writes data A into the cache.

Now let's analyze the **deficiencies of the Cache Aside Pattern**.

**Deficiency 1: The issue that the first request for data is definitely not in the cache.**

Solution: Hot data can be preloaded into the cache.

**Deficiency 2: Frequent write operations can lead to data being frequently deleted from the cache, which affects the cache hit rate.**

Solutions:

- For scenarios requiring strong consistency between the database and cache: Update the cache when updating the db, but we need to add a lock/distributed lock to ensure thread safety when updating the cache.
- For scenarios where temporary inconsistency between the database and cache is acceptable: Update the cache when updating the db, but set a relatively short expiration time for the cache, which can ensure that even if data is inconsistent, the impact is minimal.

### Read/Write Through Pattern

In the Read/Write Through Pattern, the server treats the cache as the primary data store, reading data from it and writing data into it. The cache service is responsible for reading and writing this data to the db, thereby reducing the responsibilities of the application.

This cache read/write strategy is also rarely seen in our development process. Aside from performance impacts, it is likely because the distributed cache we often use, Redis, does not provide the functionality for the cache to write data to the db.

**Write (Write Through):**

- First, check the cache; if it does not exist, directly update the db.
- If it exists in the cache, first update the cache, then the
