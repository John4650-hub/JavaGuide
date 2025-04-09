I don't...
Title: Summary of common SQL interviews (4)
Category: Database
Tag:

- Database
- SQL
  I don't...

> The title comes from: [Cowside - SQL Progress Challenge](https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=240)

Harder or more difficult topics can be determined on their own merits and interview needs.

# Special window function

MySQL 8.0 has supported the window function, which is common in MySQL and its use:

`ROW_NUMBER()`: Allocating a single integer value for each line of the search results.

```sql
SELECT col1, col2, ROW_NUMBER() OVER (ORDER BY col1) AS row_num
FROM table;
```

`RANK()`: Calculates the ranking of each line in the ranking results.

```sql
SELECT col1, col2, RANK() OVER (ORDER BY col) AS ranking
FROM table;
```

`DENSE_RANK()`: Calculates the ranking of each row in the ranking results and retains the same ranking.

```sql
SELECT col1, col2, DENSE_RANK() OVER (ORDER BY col1 DESC) AS ranking
FROM table;
```

`NTILE(n)`: Splits the results into n buckets and assigns a bucket number to each.

```sql
SELECT col1, col2, NTILE(4) OVER (ORDER BY col1) AS bucket
FROM table;
```

`SUM()`, `AVG()`, `COUNT()`, `MIN()`, `MAX()`: These aggregate functions may also be used in conjunction with window functions to calculate the sum, average, count, minimum, and maximum values of the specified columns in the window.

```sql
SELECT col1, col2, SUM(col1) OVER() AS sum_col
FROM table;
```

`LEAD()` and `LAG()`: The LEAD function is used to obtain the value of a row after the current row, and the LAG function is used to obtain the value of a row before the current row.

```sql
SELECT col1, col2, LEAD(col1, 1) OVER (ORDER BY col1) AS next_col1,
LAG(col1, 1) OVER (ORDER BY col1) AS prev_col1
FROM table;
```

`FIRST_VALUE()` and `LAST_VALUE()`: FIRST_VALUE functions are used to obtain the first value of the specified column in the window, and the LAST_VALUE function is used to obtain the last value of the specified column in the window.

```sql
SELECT col1, col2, FIRST_VALUE(col2) OVER (PARTITION BY col1 ORDER BY col2) AS first_val,
LAST_VALUE(col2) OVER (PARTITION BY col1 ORDER BY col2) AS last_val
FROM table;
```

The window function usually needs to be used in conjunction with the OVER clause to define the window size, sorting rules, and grouping.

# I'll score the top three for each class

**Description**:

`examining_info` (`exam_id` test ID, `tag` test category, `difficulty` test difficulty, `duration` examination length, `release_time` release time):

## I'm sorry.

1 | 901 | SQL | Hard | 60 | 2021-09-01 06:00
2 | 902 | SQL | Hard | 60 | 2021-09-01 06:00
3 | 903 | Algorithm | 80 | 2021-09-01 10:00 |

`exam_record` (`uid` user ID, `exam_id` test ID, `start_time` start time, `submit_time` time, `score` score):

I'm sorry, I'm sorry.
| \- | - | - | - | - | - | - | - | | | | | | | | | | | | | | | | | |
1 | 1001 | 9001 | 2021-09-01 | 2021-09-01 |
2 | 1002 | 9001 | 2021-09-01 01:01 | 2021-09-01 01:31 |
3 | 1002 | 902 | 2021-09-01 12:01:01 | 2021-09-01 12:31:01 | 81 |
4 | 1003 | 9001 | 2021-09-01 19:01:01 | 2021-09-01 19:40:01 | 86 |
5 | 1003 | 902 | 2021-09-01 12:01:01 | 2021-09-01 12:31:51 | 89 |
6 | 1004 | 9001 | 2021-09-01
