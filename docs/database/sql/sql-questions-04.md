---
title: Summary of Common SQL Interview Questions (4)
category: Database
tag:
  - Database Basics
  - SQL
---

> Source of the questions: [Niuke Question Bank - SQL Advanced Challenge](https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=240)

For more difficult questions, you can decide whether to skip them based on your actual situation and interview needs.

## Specialized Window Functions

MySQL 8.0 introduced support for window functions. Below are common window functions in MySQL and their usage:

1. `ROW_NUMBER()`: Assigns a unique integer value to each row in the result set.

```sql
SELECT col1, col2, ROW_NUMBER() OVER (ORDER BY col1) AS row_num
FROM table;
```

2. `RANK()`: Calculates the rank of each row in the sorted result.

```sql
SELECT col1, col2, RANK() OVER (ORDER BY col1 DESC) AS ranking
FROM table;
```

3. `DENSE_RANK()`: Calculates the rank of each row in the sorted result, preserving the same rank for ties.

```sql
SELECT col1, col2, DENSE_RANK() OVER (ORDER BY col1 DESC) AS ranking
FROM table;
```

4. `NTILE(n)`: Divides the result into n evenly distributed buckets and assigns an identifier to each bucket.

```sql
SELECT col1, col2, NTILE(4) OVER (ORDER BY col1) AS bucket
FROM table;
```

5. `SUM()`, `AVG()`, `COUNT()`, `MIN()`, `MAX()`: These aggregate functions can also be used with window functions to calculate the sum, average, count, minimum, and maximum of specified columns within the window.

```sql
SELECT col1, col2, SUM(col1) OVER () AS sum_col
FROM table;
```

6. `LEAD()` and `LAG()`: The LEAD function is used to get the value of a row at a specified offset after the current row, while the LAG function is used to get the value of a row at a specified offset before the current row.

```sql
SELECT col1, col2, LEAD(col1, 1) OVER (ORDER BY col1) AS next_col1,
                 LAG(col1, 1) OVER (ORDER BY col1) AS prev_col1
FROM table;
```

7. `FIRST_VALUE()` and `LAST_VALUE()`: The FIRST_VALUE function retrieves the first value of a specified column within the window, while the LAST_VALUE function retrieves the last value of a specified column within the window.

```sql
SELECT col1, col2, FIRST_VALUE(col2) OVER (PARTITION BY col1 ORDER BY col2) AS first_val,
                 LAST_VALUE(col2) OVER (PARTITION BY col1 ORDER BY col2) AS last_val
FROM table;
```

Window functions typically need to be used in conjunction with the OVER clause to define the size of the window, sorting rules, and grouping methods.

### Top Three Scores for Each Exam Type

**Description**:

There is an exam information table `examination_info` (`exam_id` Exam ID, `tag` Exam Category, `difficulty` Exam Difficulty, `duration` Exam Duration, `release_time` Release Time):

| id  | exam_id | tag       | difficulty | duration | release_time        |
| --- | ------- | --------- | ---------- | -------- | ------------------- |
| 1   | 9001    | SQL       | hard       | 60       | 2021-09-01 06:00:00 |
| 2   | 9002    | SQL       | hard       | 60       | 2021-09-01 06:00:00 |
| 3   | 9003    | Algorithm | medium     | 80       | 2021-09-01 10:00:00 |

There is an exam record table `exam_record` (`uid` User ID, `exam_id` Exam ID, `start_time` Start Time, `submit_time` Submit Time, `score` Score):

| id  | uid  | exam_id | start_time          | submit_time         | score |
| --- | ---- | ------- | ------------------- | ------------------- | ----- |
| 1   | 1001 | 9001    | 2021-09-01 09:01:01 | 2021-09-01 09:31:00 | 78    |
| 2   | 1002 | 9001    | 2021-09-01 09:01:01 | 2021-09-01 09:31:00 | 81    |
| 3   | 1002 | 900     |                     |                     |       |
