I don't...
title: MySQL Implementation Plan Analysis
Category: Database
Tag:

- MySQL
  Head:
- Meta.
- Name: keywords
  Content: MySQL Foundation, MySQL Implementation Plan, EXPLAIN, Query Optimizer
- Meta.
- Name: description
  Content: An implementation plan refers to a SQL statement that is specific to the execution of an optimization of the MySQL query optimizer. The first step in optimizing SQL should be to read the SQL implementation plan.
  I don't...

> This paper is from MySQL technology, which is supplemented by JavaGuide. Original address: <https://mp.weixin.qq.com/s/d5owNLtXBEPT31sSH4g>

The first step in optimizing SQL should be to read the SQL implementation plan. This article brings us together to learn about MySQL's 'EXPLAIN' implementation plan.

What's the plan of implementation?

**Implementation plan** refers to a SQL statement, after optimization of **MySQL query optimizer**.

Implementation plans are usually used for SQL performance analysis, optimization, etc. The results of the `EXPLAIN` provide information such as the search order of the data tables, the type of operation of the data query operation, which indices can be hit, which ones actually hit, and how many rows of each data table are searched.

# How to get an implementation plan?

MySQL provided us with an `EXPLAIN` order to obtain information on the implementation plan.

It is to be noted that the `EXPLAIN` statement does not really execute the statement, but analyzes the statement through the query optimizer, identifies the best search options, and displays the corresponding information.

The `EXPLAIN` Implementation Plan supports the statements `SELECT`, `DELETE`, `INSERT`, `REPLACE`, and `UPDATE`. We are generally used to analyze the `SELECT` query statement, which is very simple and has the following syntax:

```sql
EXPLAIN SELECT query statement;
```

Let us briefly look at the implementation plan for the next query statement:

```sql
SELECT * FROM dept_emp WHERE emp_no IN (SELECT emp_no FROM dept_emp GROUP BY emp_no HAVING COUNT(emp_no) > 1);
```

As can be seen, the results of the implementation plan include 12 columns, the meaning of which is summarized in the table below:

**Listed Meaning**

| Column        | Meaning                                                                         |
| ------------- | ------------------------------------------------------------------------------- |
| id            | Sequence identifier for SELECT queries                                          |
| select_type   | The type of SELECT                                                              |
| table         | The table name used                                                             |
| partitions    | Matching partition, value NULL for tables without partition                     |
| type          | Table access method                                                             |
| possible_keys | Possible index                                                                  |
| key           | Actually used index                                                             |
| key_len       | Length of selected index                                                        |
| ref           | Column or constant for comparison with index when using index equivalence query |
| rows          | Expected number of lines to read                                                |
| Extra         | Percentage of records retained after filtering by table conditions              |

# How to analyze EXPLAIN results?

In order to analyze the results of the implementation of the `EXPLAIN` statement, we need to understand the key fields in the implementation plan.

# id

`SELECT` identifier to identify the order of execution of each `SELECT` statement.

If the id is the same, proceed from top to bottom. The greater the value of id, the higher the priority of execution; the value can be NULL if rows refer to the combined results of other rows.

# select_type

The types of queries, which are used mainly to distinguish between common inquiries, joint inquiries, sub-queries, etc., are common values:

- **SIMPLE**: Simple query, does not contain UNION or sub-queries.
- **PRIMARY**: If a query contains sub-queries or other parts, the outer level SELECT will be marked as PRIMARY.
- **SUBQUERY**: First SELECT in the sub-reference.
- **UNION**: In the UNION statement, UNION appeared after.
- **DERIVED**: Sub-query in FROM will be marked as DERIVED.
- **UNION RESULT**: Results of UNION query.

# table

Querying for a table name with a corresponding name for each line, which, in addition to the normal table, may also be the following values:

- **<derivedN>**: This line quotes the results of id UNION for M and N;
- **<derivedN>**: This line quotes the derivatives of id's table as N. The derivative table may result from a sub-query from the FROM statement.
- **<subqueryN>**: This row refers to id's table for N for sub-references.

# type (important)

Query the type of execution, describing how the query was executed. The order of all values from the best to the worst is:

System > const > eq_ref > fulltext > ref > index_merge > unique_subquery > index_subquery > range > index > all

The following are the most common types of meaning:

- **system**: If the
