---
title: Introduction to Deep Pagination and Optimization Suggestions
category: High Performance
head:
  -   - meta
      - name: keywords
        content: Deep Pagination
  -   - meta
      - name: description
        content: The scenario where the query offset is excessively large is referred to as deep pagination, which leads to lower query performance. Deep pagination can be optimized using techniques such as range queries, subqueries, INNER JOIN lazy association, and covering indexes.
---

## Introduction to Deep Pagination

The scenario where the query offset is excessively large is referred to as deep pagination, which leads to lower query performance. For example:

```sql
# MySQL skips 1,000,000 records without utilizing the index, then retrieves 10 records.
SELECT * FROM t_order ORDER BY id LIMIT 1000000, 10
```

## Reasons for Deep Pagination Issues

When the query offset is too large, the MySQL query optimizer may choose a full table scan instead of using the index to optimize the query. This is because scanning the index and skipping a large number of records may be more resource-intensive than performing a direct full table scan.

![Deep Pagination Issues](https://oss.javaguide.cn/github/javaguide/mysql/deep-pagination-phenomenon.png)

The critical point for this excessively large query offset may vary between different machines, depending on multiple factors, including hardware configuration (such as CPU performance, disk speed), table size, index types, and statistical information.

![Critical Point for Full Table Scan](https://oss.javaguide.cn/github/javaguide/mysql/deep-pagination-phenomenon-critical-point.png)

MySQL’s query optimizer employs a cost-based strategy to select the optimal query execution plan. It determines whether to use index scanning or full table scanning based on the costs of CPU and I/O. If the optimizer decides that the cost of a full table scan is lower, it will forgo using the index. However, even with a large offset, if a covering index is used in the query, MySQL might still utilize the index, avoiding the need to access the data rows again.

## Optimization Suggestions for Deep Pagination

Taking the MySQL database as an example, here are some ways to optimize deep pagination.

### Range Query

When continuity of IDs can be ensured, using ID ranges for pagination is a good solution:

```sql
# Query data within a specified ID range
SELECT * FROM t_order WHERE id > 100000 AND id <= 100010 ORDER BY id
# You can also query the next page using the ID of the last record from the previous query:
SELECT * FROM t_order WHERE id > 100000 LIMIT 10
```

This ID range-based deep pagination optimization method has significant limitations:

1. **High Requirement for ID Continuity**: In actual projects, database auto-increment IDs often become non-continuous for various reasons (such as data deletion, transaction rollbacks), making it difficult to ensure continuity.
1. **Sorting Issues**: If the query needs to be sorted by other fields (such as creation time, update time, etc.) instead of sorting by ID, then this method is no longer applicable.
1. **Concurrent Scenarios**: In high concurrency scenarios, relying solely on the ID of the last record from the previous query for pagination can easily lead to data duplication or omission issues.

### Subquery

We can first retrieve the primary key values corresponding to the first parameter of limit, and then filter and limit based on this primary key value, which will be more efficient.

The Alibaba "Java Development Handbook" also describes this:

> Use lazy association or subqueries to optimize excessive pagination scenarios.
>
> ![](https://oss.javaguide.cn/github/javaguide/mysql/alibaba-java-development-handbook-paging.png)

```sql
# Use a subquery to get the starting value of id, transferring the LIMIT 1000000 condition to the subquery
SELECT * FROM t_order WHERE id >= (SELECT id FROM t_order where id > 1000000 limit 1) LIMIT 10;
```

**How It Works**:

1. The subquery `(SELECT id FROM t_order where id > 1000000 limit 1)` quickly locates the 1,000,001st record using the primary key index and returns its ID value.
1. The main query `SELECT * FROM t_order WHERE id >= ... LIMIT 10` uses the starting ID returned from the subquery as a filter condition, using `id >=` to retrieve the subsequent 10 records starting from that ID.

However, the result of the subquery generates a temporary table, which can impact performance. Therefore, large-scale use of subqueries should be avoided. Moreover, this method is only suitable when IDs are in ascending order. In complex pagination scenarios, filtering conditions may be needed to identify matching IDs, resulting in discrete and non-continuous IDs.

Of course, we can also use subqueries to retrieve the target page's set of IDs and then fetch content based on this ID set, but this approach is very cumbersome compared to using INNER JOIN for lazy association.

### Lazy Association

Lazy association is similar to the optimization idea of subqueries, where `LIMIT` operations are moved to the primary key index tree to reduce the number of back-table operations. Compared to directly using subqueries, lazy association integrates the subquery results into the main query through `INNER JOIN`, avoiding the potential creation of temporary tables by subqueries. When executing `INNER JOIN`, the MySQL optimizer can utilize indexes for efficient join operations (like index scanning or other optimization strategies), thus usually achieving better performance in deep pagination scenarios than directly using subqueries.

```sql
-- Use INNER JOIN for lazy association
SELECT t1.*
FROM t_order t1
INNER JOIN (SELECT id FROM t_order where id > 1000000 LIMIT 10) t2 ON t1.id = t2.id;
```

**How It Works**:

1. The subquery `(SELECT id FROM t_order where id > 1000000 LIMIT 10)` quickly locates the IDs of the 10 target records using the primary key index.
1. Using `INNER JOIN`, the subquery results are associated with the main table `t_order`, retrieving complete record data.

In addition to using INNER JOIN, you can also connect subqueries with commas.

```sql
-- Use a comma for lazy association
SELECT t1.* FROM t_order t1,
(SELECT id FROM t_order where id > 1000000 LIMIT 10) t2
WHERE t1.id = t2.id;
```

[!NOTE]
Although connecting subqueries with commas can achieve similar effects, it is recommended to use the more standardized `INNER JOIN` syntax for code readability and maintainability.
### Covering Index

A query method where all required fields are included in the index is known as a covering index.

**Benefits of Covering Index**:

- **Avoids InnoDB tables from performing second index queries, which is a back-table operation**: InnoDB stores data in the order of the clustered index. For InnoDB, the secondary index stores the primary key information in its leaf nodes. If data is queried using the secondary index, after finding the corresponding key value, a secondary query via the primary key is still required to retrieve the actual data we need. However, in covering indexes, all data can be accessed from the secondary index's key values, avoiding the need for a secondary query (back-table access), thereby reducing I/O operations and improving query efficiency.
- **Transforms random I/O into sequential I/O, speeding up query efficiency**: Since covering indexes are stored in key value order, for I/O intensive range queries, it significantly reduces the I/O involved in randomly reading each row of data from disk, thus making disk random reads into index lookups in sequential order.

```sql
# If only the id, code, and type columns are needed, a covering index can be established for code and type
SELECT id, code, type FROM t_order
ORDER BY code
LIMIT 1000000, 10;
```

**⚠️Note**:

- When the result set of a query accounts for a large proportion of the total rows in the table, MySQL’s query optimizer may opt to forgo using the index, automatically switching to a full table scan.
- While you can use `FORCE INDEX` to compel the query optimizer to use the index, this might lead to the optimizer being unable to select a better execution plan, and the outcome may not always be ideal.

## Conclusion

This article summarizes several common optimization schemes for deep pagination:

1. **Range Query**: Pagination based on ID continuity, using the ID of the last record from the previous page to fetch data for the next page. Suitable for scenarios where IDs are continuous and queries are sorted by ID, but limited in cases where IDs are non-continuous or sorted by other fields.
1. **Subquery**: First, the starting primary key value for pagination is retrieved through a subquery, then filtered based on the primary key for pagination. It utilizes primary key indexing to improve efficiency, but subqueries create temporary tables, which may lead to poor performance in complex scenarios.
1. **Lazy Association (INNER JOIN)**: Utilizing `INNER JOIN` to shift pagination operations to the primary key index, reducing the incidence of back-table operations. Lazy association typically performs better than subqueries, making it suitable for pagination queries involving large data volumes.
1. **Covering Index**: Directly retrieving required fields through an index, avoiding back-table operations and minimizing I/O overhead, suitable for queries that target specific fields. However, when the result set is large, MySQL may choose a full table scan.

## References

- Discussing how to solve MySQL deep pagination issues - A Boy Who Picks Snails: <https://juejin.cn/post/7012016858379321358>
- Introduction to deep pagination in databases and optimization schemes - JD Retail Technology: <https://mp.weixin.qq.com/s/ZEwGKvRCyvAgGlmeseAS7g>
- MySQL deep pagination optimization - Dewu Technology: <https://juejin.cn/post/6985478936683610149>
