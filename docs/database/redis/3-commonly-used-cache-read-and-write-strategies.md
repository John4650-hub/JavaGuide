I don't...
title: 3 Common Cache Reading and Writing Strategies
Category: Database
Tag:

- Redis
  I don't...

Seeing a lot of small partners' résumés with “**proficient use of the Cache**,” but when I asked “**three commonly used reading and writing strategies**,”

It seems to me that the reason for this problem is that, while we're studying Redis, we may have simply written some demos, not focused on the slow-down reading and writing strategy, or simply not known it.

However, understanding 3 common CBC strategies can be very helpful for the use of CVs in practice and for being questioned about CVs in interviews!

**The three models described below have advantages and disadvantages; there are no best models, and you should choose a model for your own cache reading and writing according to the specific business scene.**

# Cache Aside Pattern

**Cache Aside Pattern is a much easier-to-read and write mode we normally use, which is more suitable for handling more read requests.**

The service end in Cache Aside Pattern requires both db and cache, and is based on db.

Let's take a look at the slow reading and writing steps in this strategy mode.

**Write**:

- Update db first.
- Then remove the cache directly.

A simple picture was drawn to help you understand the steps.

![Write Steps](https://oss.javaguide.cn/github/javaguide/database/redis/cache-aside-write.png)

**Read**:

- Read data from the cache and return it directly.
- Read data back from db if you cannot read from cache.
- Put the data in the cache.

A simple picture has been drawn to help you understand the steps of reading.

![Read Steps](https://oss.javaguide.cn/github/javaguide/database/redis/cache-aside-read.png)

It is not enough for you to simply understand the above; we need to understand the rationale.

For example, the interviewer is likely to ask: "**In the process of writing data, can you delete the cache and update db?**"

**Answer:** That's definitely not gonna work! This may cause problems with data inconsistencies between databases (db) and Cache.\*\*

For example, request 1 for data A, request 2 for subsequent data A is likely to create data inconsistencies.

This process can be described briefly as:

> Request 1 first removes the A data from the Cache -> Request 2 reads Data from db -> Request 1 updates the A data in db.

When you answer that question, the interviewer may immediately follow up with the question: "**Update db in the process of writing the data and delete the cache.**”

**Answer:** Theoretically, the problem of data inconsistency may still arise, but the probability is very low because the cache is written much faster than in the database.

For example: Request 1 first reads data A, request 2 then writes data A, and data A may also raise data inconsistencies if it is not in the cache before the request is made.

This process can be described briefly as:

> Request 1 reads Data A -> Request 2 updates Data A in db (no data A in the cache at this time, therefore no action to remove the cache) -> Request 1 writes Data A to Cache.

Now let's analyze the shortcomings of **Cache Aside Pattern**.

**Deficiency 1: the problem of not having data requested for the first time.**

Solutions: Hotspot data can be pre-loaded into cache.

**Deficiency 2: more frequent writing leads to frequent deletion of the data in cache, which affects the Cache Hit Rate.**

Solutions:

- Databases and caches are well aligned: the same is updated when db is updated, but we need to add a lock/distribution lock to ensure that there is no thread safety issue when updating cache.
- A short period of time allows for a scenario where the database and the cache data are not consistent: the same is updated when db is updated, but a shorter expiry period is added to the cache, so that even if the data are not consistent, the impact will be less.

# Read/Write Through Pattern

The Read/Write Through Pattern server considers cache as the primary data repository from which the data is read and written. Cache services are responsible for reading and writing this data to db, thus reducing the responsibility of the application.

The small partners in this cache reading and writing strategy should also have found it very rare in the development process. It drops the performance effect, probably because the distributed cache that we often use does not provide the cache to write to db.

**Write Through:**

- Check first; if Cache does not exist, directly update db.
- If Cache exists, update the cache and then the cache service updates db (**synchronized updates of cache and db**).

A simple picture was drawn to help you understand the steps.

![Write Through Steps](https://oss.javaguide.cn/github/javaguide/database/redis/write-through.png)

\*\*Read Through:
