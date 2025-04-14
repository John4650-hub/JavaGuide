---
title: MySQL Execution Plan Analysis
category: Database
tag:
  - MySQL
head:
  -   - meta
      - name: keywords
        content: MySQL Basics, MySQL Execution Plan, EXPLAIN, Query Optimizer
  -   - meta
      - name: description
        content: The execution plan refers to the specific execution method of an SQL statement after being optimized by the MySQL query optimizer. The first step in optimizing SQL should be to understand the SQL execution plan.
---

> This article is from the public account MySQL Technology, with enhancements by JavaGuide. Original article link: <https://mp.weixin.qq.com/s/d5OowNLtXBGEAbT31sSH4g>

The first step in optimizing SQL should be to understand the SQL execution plan. In this article, we will learn about MySQL `EXPLAIN` execution plan related knowledge together.

## What is an Execution Plan?

**Execution Plan** refers to the specific execution method of an SQL statement after being optimized by the **MySQL Query Optimizer**.

Execution plans are typically used in SQL performance analysis, optimization, and other scenarios. By examining the results of `EXPLAIN`, one can understand information such as the order of table queries, the types of data query operations, which indexes can be hit, which indexes will actually be hit, and how many rows from each table are being queried.

## How to Obtain an Execution Plan?

MySQL provides us with the `EXPLAIN` command to obtain information related to the execution plan.

It is important to note that the `EXPLAIN` statement does not actually execute the related statement; instead, it analyzes the statement through the query optimizer to find the optimal query plan and displays the corresponding information.

The `EXPLAIN` execution plan supports `SELECT`, `DELETE`, `INSERT`, `REPLACE`, and `UPDATE` statements. It is generally used more for analyzing `SELECT` queries, and it is very simple to use, with the syntax as follows:

```sql
EXPLAIN + SELECT query statement;
```

Let's take a look at the execution plan for a simple query statement:

```sql
mysql> explain SELECT * FROM dept_emp WHERE emp_no IN (SELECT emp_no FROM dept_emp GROUP BY emp_no HAVING COUNT(emp_no)>1);
+----+-------------+----------+------------+-------+-----------------+---------+---------+------+--------+----------+-------------+
| id | select_type | table    | partitions | type  | possible_keys   | key     | key_len | ref  | rows   | filtered | Extra       |
+----+-------------+----------+------------+-------+-----------------+---------+---------+------+--------+----------+-------------+
|  1 | PRIMARY     | dept_emp | NULL       | ALL   | NULL            | NULL    | NULL    | NULL | 331143 |   100.00 | Using where |
|  2 | SUBQUERY    | dept_emp | NULL       | index | PRIMARY,dept_no | PRIMARY | 16      | NULL | 331143 |   100.00 | Using index |
+----+-------------+----------+------------+-------+-----------------+---------+---------+------+--------+----------+-------------+
```

As we can see, the execution plan result contains 12 columns, and the meanings of each column are summarized in the table below:

| **Column Name** | **Meaning**                                                            |
| --------------- | ---------------------------------------------------------------------- |
| id              | Identifier for the sequence of the SELECT query                        |
| select_type     | The type of query corresponding to the SELECT keyword                  |
| table           | The name of the table used                                             |
| partitions      | Matching partitions; NULL for non-partitioned tables                   |
| type            | The access method for the table                                        |
| possible_keys   | Possible indexes that could be used                                    |
| key             | The actual index used                                                  |
| key_len         | The length of the selected index                                       |
| ref             | The column or constant compared with the index in equality queries     |
| rows            | The estimated number of rows to be read                                |
| filtered        | The percentage of records retained after filtering by table conditions |
| Extra           | Additional information                                                 |

## How to Analyze EXPLAIN Results?

To analyze the execution results of the `EXPLAIN` statement, we need to understand the important fields in the execution plan.

### id

The `SELECT` identifier is used to identify the execution order of each `SELECT` statement.

If the id is the same, they are executed in order from top to bottom. If the id is different, a higher id value indicates a higher execution priority. If a row references the union result of other rows, this value can be NULL.

### select_type

The type of query, mainly used to distinguish between simple queries, union queries, subqueries, and other complex queries. Common values include:

- **SIMPLE**: A simple query that
