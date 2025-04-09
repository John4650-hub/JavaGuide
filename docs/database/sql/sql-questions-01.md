I don't...
Title: Summary of common SQL interviews (1)
Category: Database
Tag:

- Database
- SQL
  I don't...

> The title comes from: [Cowside-SQL must know](https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=298)

# Retrieving data

`SELECT` is used to query data from a database.

# Retrieving all IDs from the Customers Table

The current table `Customers` is as follows:

| ID  |
| --- |
| Ace |
| B   |
| C-1 |

Write SQL statements to retrieve all `cut_id` from `Customers` tables.

Answer:

```sql
SELECT cut_id
FROM Customers
```

# Retrieving and listing the list of ordered products

Table `OrderItems` contains non-empty columns `prod_id` for commodities and includes all goods ordered (some have been ordered repeatedly).

| prod_id |
| ------- |
| A1-O    |
| A2      |
| A3-O3   |
| A4      |
| A5      |
| A6      |
| A7      |

Preparation of SQL statements to retrieve and list the redacted list of all ordered commodities (`prod_id`).

Answer:

```sql
SELECT DISTINCT prod_id
FROM OrderItems
```

Knowledge point: `DISTINCT` returns only different values in the column.

# Retrieving all columns

There are now `Customers` tables (the table contains a column `cut_id` representing the client id, `cut_name` representing the client).

| cut_id | cut_name         |
| ------ | ---------------- |
| 1      | A one-and-a-half |
| 2      | A two            |
| 3      | A three          |
| 4      | A four           |
| 5      | A five           |
| 6      | A six            |
| 7      | A seven          |

You need to write SQL statements to retrieve all columns.

Answer:

```sql
SELECT cut_id, cut_name
FROM Customers
```

# Sort Retrieval Data

`ORDER BY` is used to sort the results set by one or more columns. Default to sort records in ascending order, using the `DESC` keyword if they need to be sorted in descending order.

# Retrieving customer names and sorting

There are tables `Customers`, `cut_id` representing the client id, `cut_name` representing the client’s name.

| cut_id | cut_name         |
| ------ | ---------------- |
| 1      | A one-and-a-half |
| 2      | A two            |
| 3      | A three          |
| 4      | A four           |
| 5      | A five           |
| 6      | A six            |
| 7      | A seven          |

Search all customer names (`cut_name`) from `Customers` and display the results in the order from Z to A.

Answer:

```sql
SELECT cut_name
FROM Customers
ORDER BY cut_name DESC
```

# Sort customer ID and date

There are `Orders` tables:

| order_num | cut_id | order_date       |
| --------- | ------ | ---------------- |
| A1        | 1      | 2022-01-01 10:00 |
| A2        | 2      | 2022-01-02 11:00 |
| A3        | 1      | 2022-01-03 12:00 |
| A4        | 3      | 2022-01-04 13:00 |
| A5        | 2      | 2022-01-05 14:00 |
| A6        | 1      | 2022-01-06 15:00 |
| A7        | 3      | 2022-01-07 16:00 |

Preparation of SQL statements to retrieve customer ID (`cut_id`) and order number (`order_num`) from the `Orders` table, and first sort the results by customer ID and then in reverse by the date of the order.

Answer:

```sql
SELECT cut_id, order_num
FROM Orders
ORDER BY cut_id, order_date DESC
```

Knowledge point: `ORDER BY` in the order of multiple columns, first in the rows and later in the rows. And different columns can have different sorting rules.

## Sort by quantity and price

Assuming that there is an `OrderItems` table:

| prod_id | quantity | item_price |
| ------- | -------- | ---------- |
| A1      | 100      | 10         |
| A2      | 200      | 20         |
| A3      | 150      | 15         |

Preparation of SQL statements showing the number (`quantity`) and price (`item_price`) of the `OrderItems` tables
