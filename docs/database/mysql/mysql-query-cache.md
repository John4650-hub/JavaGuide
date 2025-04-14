---
title: MySQL Query Cache Explained
category: Database
tag:
  - MySQL
head:
  -   - meta
      - name: keywords
        content: MySQL Query Cache, MySQL Cache Mechanism Memory Management
  -   - meta
      - name: description
        content: To improve the response speed of identical query statements, MySQL Server calculates a hash value for the query statement. MySQL Server does not process the SQL; the SQL must be exactly the same for the hash value to match. After obtaining the hash value, it matches the query result in the query cache using that hash value. Although the query cache in MySQL can enhance the query performance of the database, it also brings additional overhead, as a cache operation must be performed after each query, and it must be destroyed after it becomes invalid.
---

Caching is an effective and practical means of optimizing system performance, and it is used to some extent in operating systems, various software, and websites.

However, experienced DBAs recommend disabling the built-in Query Cache in MySQL in production environments. Moreover, starting from MySQL 5.7.20, the query cache has been deprecated by default. In MySQL 8.0 and later, the query cache feature has been completely removed.

Why is this the case? Is the query cache really that useless?

With the following questions in mind, we officially enter this article.

- What is MySQL Query Cache? What is its scope of application?
- What are the caching rules in MySQL?
- What are the advantages and disadvantages of MySQL caching?
- How does MySQL caching affect performance?

## Introduction to MySQL Query Cache

The architecture of MySQL is shown in the diagram below:

![](https://oss.javaguide.cn/github/javaguide/mysql/mysql-architecture.png)

To improve the response speed of identical query statements, MySQL Server calculates a hash value for the query statement. MySQL Server does not process the SQL; the SQL must be exactly the same for the hash value to match. After obtaining the hash value, it matches the query result in the query cache using that hash value.

- If there is a match (hit), the result set of the query is returned directly to the client without needing to parse or execute the query.
- If there is no match (miss), the hash value and result set are stored in the query cache for future use.

In other words, **when a query statement (select) reaches MySQL Server, it first checks the query cache. If it has been executed before, it directly returns the result set to the client.**

![](https://oss.javaguide.cn/javaguide/13526879-3037b144ed09eb88.png)

## MySQL Query Cache Management and Configuration

You can view information related to the query cache using the command `show variables like '%query_cache%'`.

Before version 8.0, the printed information might look like this:

```bash
mysql> show variables like '%query_cache%';
+------------------------------+---------+
| Variable_name                | Value   |
+------------------------------+---------+
| have_query_cache             | YES     |
| query_cache_limit            | 1048576 |
| query_cache_min_res_unit     | 4096    |
| query_cache_size             | 599040  |
| query_cache_type             | ON      |
| query_cache_wlock_invalidate | OFF     |
+------------------------------+---------+
6 rows in set (0.02 sec)
```

After version 8.0 and later, the printed information looks like this:

```bash
mysql> show variables like '%query_cache%';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| have_query_cache | NO    |
+------------------+-------+
1 row in set (0.01 sec)
```

Here, we explain the information printed by the command `show variables like '%query_cache%';` before version 8.0.

- **`have_query_cache`:** Indicates whether this MySQL Server supports query cache. If it is YES, it means support; otherwise, it does not support.
- **`query_cache_limit`:** The maximum query result for MySQL query cache. Query results larger than this value will not be cached.
- **`query_cache_min_res_unit`:** The minimum size (in bytes) of the allocated block for the query cache. When a query is executed, MySQL stores the query result in the query cache, but if the result to be saved is large and exceeds the value of `query_cache_min_res_unit`, MySQL will retrieve the result while saving it, meaning that it may need to perform multiple memory allocation operations in a single query. Properly adjusting `query_cache_min_res_unit` can optimize memory usage.
- **`query_cache_size`:** The amount of memory allocated for caching query results, measured in bytes, and the value must be a multiple of
