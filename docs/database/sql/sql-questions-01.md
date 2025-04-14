---
title: Summary of Common SQL Interview Questions (1)
category: Database
tag:
  - Database Basics
  - SQL
---

> Source of questions: [Niuke Question Bank - SQL Essentials](https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=298)

## Data Retrieval

`SELECT` is used to query data from the database.

### Retrieve all IDs from the Customers table

The existing `Customers` table is as follows:

| cust_id |
| ------- |
| A       |
| B       |
| C       |

Write an SQL statement to retrieve all `cust_id` from the `Customers` table.

Answer:

```sql
SELECT cust_id
FROM Customers
```

### Retrieve and list the ordered product list

The `OrderItems` table contains a non-null column `prod_id` representing the product ID, which includes all ordered products (some may have been ordered multiple times).

| prod_id |
| ------- |
| a1      |
| a2      |
| a3      |
| a4      |
| a5      |
| a6      |
| a7      |

Write an SQL statement to retrieve and list all distinct ordered products (`prod_id`).

Answer:

```sql
SELECT DISTINCT prod_id
FROM OrderItems
```

Knowledge point: `DISTINCT` is used to return unique different values in a column.

### Retrieve all columns

Now there is a `Customers` table (which contains the column `cust_id` representing customer ID and `cust_name` representing customer name)

| cust_id | cust_name |
| ------- | --------- |
| a1      | andy      |
| a2      | ben       |
| a3      | tony      |
| a4      | tom       |
| a5      | an        |
| a6      | lee       |
| a7      | hex       |

You need to write an SQL statement to retrieve all columns.

Answer:

```sql
SELECT cust_id, cust_name
FROM Customers
```

## Sorting Data Retrieval

`ORDER BY` is used to sort the result set by one or more columns. By default, records are sorted in ascending order; to sort records in descending order, you can use the `DESC` keyword.

### Retrieve customer names and sort

There is a `Customers` table, where `cust_id` represents customer ID and `cust_name` represents customer name.

| cust_id | cust_name |
| ------- | --------- |
| a1      | andy      |
| a2      | ben       |
| a3      | tony      |
| a4      | tom       |
| a5      | an        |
| a6      | lee       |
| a7      | hex       |

Retrieve all customer names (`cust_name`) from `Customers` and display the results in descending order from Z to A.

Answer:

```sql
SELECT cust_name
FROM Customers
ORDER BY cust_name DESC
```

### Sort by customer ID and date

There is an `Orders` table:

| cust_id | order_num | order_date          |
| ------- | --------- | ------------------- |
| andy    | aaaa      | 2021-01-01 00:00:00 |
| andy    | bbbb      | 2021-01-01 12:00:00 |
| bob     | cccc      | 2021-01-10 12:00:00 |
| dick    | dddd      | 2021-01-11 00:00:00 |

Write an SQL statement to retrieve customer ID (`cust_id`) and order number (`order_num`) from the `Orders` table, first sorting the results by customer ID, then sorting by order date in descending order.

Answer:

```sql
# Sort by column name
# Note: order_date is in descending order, not order_num
SELECT cust_id, order_num
FROM Orders
ORDER BY cust_id, order_date DESC
```

Knowledge point: When sorting multiple columns with `ORDER BY`, the first column to be sorted is placed first, and the subsequent columns follow. Different columns can have different sorting rules.

### Sort by quantity and price

Assuming there is an `OrderItems` table:

| quantity | item_price |
| -------- | ---------- |
| 1        | 100        |
| 10       | 1003       |
| 2        | 500        |

Write an SQL statement to display the quantity (`quantity`) and price (`item_price`) from the `OrderItems` table, sorted by quantity in descending order and price in descending order.

Answer:

```sql
SELECT quantity, item_price
FROM OrderItems
ORDER BY quantity DESC, item_price DESC
```

### Check SQL statement

There is a `Vendors` table:

| vend_name |
| --------- |
| Haidilao  |
| Xia       |
