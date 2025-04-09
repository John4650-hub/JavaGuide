I don't...
title: MySQL Query Cache
Category: Database
Tag:

- MySQL
  Head:
- Meta
- Name: keywords
  Contact: MySQL Query Cache, Memory Management in MySQL Cache
- Meta
- Name: description
  Grant: In order to increase the speed of response to identical query statements, MySQL Server calculates a hash value for a query statement. MySQL Server will not do anything about SQL, and SQL must be fully consistent with the hash value. When you get the hash value, match the result of the query with the hash value in the query cache. While the query cache in MySQL enhances the searchability of the database, the query also entails additional costs, with a cache operation after each query and destruction after it has failed.
  I don't...

Cache is an effective and practical tool for system optimization, which is more or less used in operating systems and in software and websites.

However, experienced DBAs suggest that MySQL's own Query Cache (Query Cache) be shut off in the production environment. Furthermore, the query cache has been deprecated since MySQL 5.7.20. It has been directly removed from MySQL 8.0 and later.

Why is that? Is it really that chicken?

With the following questions, we have entered the document.

- What is MySQL Query Cache?
- What are MySQL Cache Rules?
- What are the advantages and disadvantages of MySQL Cache?
- What does Cache do to performance in MySQL?

# MySQL Query Cache Description

MySQL structure is as follows:

![MySQL Architecture](https://oss.javaguide.cn/github/javaguide/mysql/mysql-architecture.png)

In order to increase the response speed of identical query statements, MySQL Server calculates a hash value for a query statement. MySQL Server will not do anything about SQL, and SQL must be fully consistent with the hash value. When you get the hash value, match the result of the query with the hash value in the query cache.

- If you match (hit), return the result set of the query directly to the client, without any further decomposition and execution of the query.
- If there is no match (miss), save the hash values and the result set in the query cache for later use.

That is, **a query statement (itself) will go to MySQL Server to check the query cache and, if it has been executed, return the result to the client.**

![MySQL Query Cache](https://oss.javaguide.cn/javaguide/13526879-3037b144ed09eb88.png)

# MySQL Query Cache Management and Configuration

The search for cache information can be viewed by the `SHOW VARIABLES LIKE '%query_cache%'` command.

Before version 8.0, the printed information may be as follows:

```bash
Mysql> SHOW VARIABLES LIKE '%query_cache%';
+--------------------------+---------+
| Variable_name            | Value   |
+--------------------------+---------+
| query_cache_limit        | 1048576 |
| query_cache_min_res_unit | 4096    |
| query_cache_size         | 599040  |
| query_cache_wlock_invalidate | OFF  |
+--------------------------+---------+
4 rows in set (0.02 sec)
```

In version 8.0 and later, the information printed is as follows:

```bash
Mysql> SHOW VARIABLES LIKE '%query_cache%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| have_query_cache         | NO    |
+--------------------------+-------+
1 row in set (0.01 sec)
```

Here we explain the information `SHOW VARIABLES LIKE '%query_cache%'` before 8.0; order printed out.

- **`have_query_cache`:** Does MySQL Server support the query cache? If YES, it supports it.
- **`query_cache_limit`:** The maximum size of the result set that can be cached. If the result is greater than this value, it will not be cached.
- **`query_cache_min_res_unit`:** The size (in bytes) of the smallest piece of the cache for distributing queries. When a query is made, MySQL saves the query results in the query cache, but if the result is to be saved in a larger way than `query_cache_min_res_unit`, then MySQL will perform multiple memory distribution operations. Appropriate reconciliation of `query_cache_min_res_unit` optimizes memory.
- **`query_cache_size`:** The amount of memory assigned to cache query results, in bytes, and the value must be an integer multiple of 1024. The default value is 0, i.e., the query cache is disabled.
- **`query_cache_type`:** Sets the query cache type; the default is ON. Sets the GLOBAL value to set the type for all subsequent client connections. Clients can set SESSION values to influence their own use of query caches.
- \*\*\`query_cache_wlock
