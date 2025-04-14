---
title: Summary of Common SQL Interview Questions (5)
category: Database
tag:
  - Database Basics
  - SQL
---

> Source of questions: [Niuke Question Bank - SQL Advanced Challenge](https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=240)

Difficult or challenging questions can be skipped based on your actual situation and interview needs.

## Handling Null Values

### Count the number of unfinished exams and the unfinished rate for exams with an unfinished status

**Description**:

There is an exam record table `exam_record` (`uid` User ID, `exam_id` Exam ID, `start_time` Start time, `submit_time` Submission time, `score` Score), with the following data:

| id  | uid  | exam_id | start_time          | submit_time         | score  |
| --- | ---- | ------- | ------------------- | ------------------- | ------ |
| 1   | 1001 | 9001    | 2020-01-02 09:01:01 | 2020-01-02 09:21:01 | 80     |
| 2   | 1001 | 9001    | 2021-05-02 10:01:01 | 2021-05-02 10:30:01 | 81     |
| 3   | 1001 | 9001    | 2021-09-02 12:01:01 | (NULL)              | (NULL) |

Please count the number of unfinished exams `incomplete_cnt` and the unfinished rate `incomplete_rate` for exams with an unfinished status. The output based on the example data is as follows:

| exam_id | incomplete_cnt | complete_rate |
| ------- | -------------- | ------------- |
| 9001    | 1              | 0.333         |

Explanation: Exam 9001 has 3 records, of which two are completed and one is unfinished, so the number of unfinished exams is 1, and the unfinished rate is 0.333 (retaining 3 decimal places).

**Thought Process**:

This question only requires attention to one condition with restrictions and one without; you can either query the conditions separately and then merge them, or directly perform conditional checks in the select statement.

**Answer**:

Method 1:

```sql
SELECT exam_id,
       count(submit_time IS NULL OR NULL) incomplete_cnt,
       ROUND(count(submit_time IS NULL OR NULL) / count(*), 3) complete_rate
FROM exam_record
GROUP BY exam_id
HAVING incomplete_cnt <> 0
```

Method 2:

```sql
SELECT exam_id,
       count(submit_time IS NULL OR NULL) incomplete_cnt,
       ROUND(count(submit_time IS NULL OR NULL) / count(*), 3) complete_rate
FROM exam_record
GROUP BY exam_id
HAVING incomplete_cnt <> 0
```

Both methods are valid; only the middle part differs, one counts only those that meet the condition, while the other uses `IF` directly, which is more intuitive. Finally, this `HAVING` clause explains that as long as either `complete_rate` or `incomplete_cnt` is not 0, it means there are unfinished exams.

### Average time and average score for level 0 users on difficult exams

**Description**:

There is a user information table `user_info` (`uid` User ID, `nick_name` Nickname, `achievement` Achievement value, `level` Level, `job` Job direction, `register_time` Registration time), with the following data:

| id  | uid  | nick_name | achievement | level | job       | register_time       |
| --- | ---- | --------- | ----------- | ----- | --------- | ------------------- |
| 1   | 1001 | Niuke 1   | 10          | 0     | Algorithm | 2020-01-01 10:00:00 |
| 2   | 1002 | Niuke 2   | 2100        | 6     | Algorithm | 2020-01-01 10:00:00 |

There is an exam information table `examination_info` (`exam_id` Exam ID, `tag` Exam category, `difficulty` Exam difficulty, `duration` Exam duration, `release_time` Release time), with the following data:

| id  | exam_id | tag | difficulty | duration | release_time        |
| --- | ------- | --- | ---------- | -------- | ------------------- |
| 1   | 9001    | SQL | hard       | 60       | 2020-01-01 10:00:00 |
| 2   | 9002    | SQL | easy       | 60       | 2020-01-01          |
