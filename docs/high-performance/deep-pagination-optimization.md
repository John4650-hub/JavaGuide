I don't...
title: Depth Page Presentation and Optimization
Category: High performance
Head:

- Meta.
- Name: keywords
  Contact: Depth Break
- Meta.
- Name: description
  This is what we call a depth break, which leads to low search performance. Depth pages can be optimized using range, sub-queries, INNER JOIN delayed association, over-indexing, etc.
  I don't...

# Deep Page Break Introduction

A query that is too big is called a Depth Break, which leads to lower query performance. For example:

```sql
# MySQL skips a million records without access to the index and retrieves 10 more records
SELECT * FROM _order ORDER BY id LIMIT 100000, 10;
```

# Reason for the Deep Page Break Problem

MySQL's query opts for a full-table scan instead of using an index to optimize the query when the query offset is too large. This is because scanning indexes and skipping large quantities of records may be more resource-intensive than direct full-table scanning.

![Depth Breakout Problem](https://oss.javaguide.cn/github/javaguide/mysql/deep-pagination-penomenon.png)

The threshold at which the query is overloaded on different machines may vary depending on several factors, including hardware configuration (e.g., CPU performance, disk speed), table size, index type, and statistical information.

![Depth Pagination Crime Point](https://oss.javaguide.cn/github/javaguide/mysql/deep-pagination-phenomenon-crime-point.png)

MySQL's query optimizer uses a cost-based strategy to select the best query implementation plan. It will decide whether to use an index or full-table scans based on CPU and I/O costs. If the optimizer considers the full table scan to be less expensive, it abandons the index. However, even if the amount of offset is significant, MySQL may use the index to avoid back-table operations if the covering index is used in the query.

# Depth Page Optimization Proposal

The MySQL database is used here as an example of how to optimize the depth break.

## Range Query

A page break based on the ID range is a better solution when the continuity of the ID is assured:

```sql
# Query data for specified ID range
SELECT * FROM _order WHERE id > 100000 AND id <= 100010 ORDER BY id;
# The next page of the query can also be made through ID, which records the last record of the last query:
SELECT * FROM _order WHERE id > 100000 LIMIT 10;
```

This depth page optimization based on the ID range is very limited:

1. **ID's high level of continuity**: In actual projects, the database automatically adds IDs for various reasons (e.g., data deletion, roll-back of services, etc.), which often leads to the discontinuity of IDs and makes continuity difficult.
1. **Sorting**: This method is no longer applicable if the query needs to be sorted by other fields (e.g., creation, updating, etc.) rather than by ID.
1. **Side-by-side view**: In high-symmetry settings, page breaks relying solely on the ID of the last record that the last query was made to record are prone to data repetitions or omissions.

## Sub-Query

Let's check out the primary key value for the first parameter, then filter it on this primary key value, making it more efficient.

This is also described in the Java Development Manual of Alibaba:

> Optimizes the hyperscore scenario using delayed association or sub-queries.
>
> ![Alibaba Java Development Handbook Paging](https://oss.javaguide.cn/github/javaguide/mysql/alibaba-java-development-handbook-paging.png)

```sql
# Retrieving ID's starting value by sub-queries and moving the LIMIT '100000' condition to sub-queries
SELECT * FROM t_order WHERE id >= (SELECT id FROM t_order WHERE id > 100000 LIMIT 1) LIMIT 10;
```

**Rationale**

1. Sub-Query `(SELECT id FROM _order WHERE id > 100000 LIMIT 1)` is fast-tracked to rule 100001 records using the primary index and returns its ID value.
1. The main query `SELECT * FROM _order WHERE id >= ... LIMIT 10` uses the `id >=` as a filter condition for the initial ID of the sub-query return to obtain 10 subsequent records starting with the ID.

However, the result of the sub-queries will produce a new table that will affect performance and should avoid as much as possible the use of sub-queries. And, this method only applies when IDs are in order. In complex page-segregation scenarios, it is often necessary to filter eligible IDs through filtering conditions, at which point the IDs are discrete and discontinuous.

Of course, we can also use sub-queries to get the IDs from the target sub-pages first, and then get the content from the IDs, but this is a very cumbersome writing, rather than using INNER JOIN to
