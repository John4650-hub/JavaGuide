---
title: Summary of Common SQL Interview Questions (3)
category: Database
tag:
  - Database Basics
  - SQL
---

> Source of questions: [Niuke Question Bank - SQL Advanced Challenge](https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=240)

You can decide whether to skip difficult or challenging questions based on your actual situation and interview needs.

## Aggregate Functions

### Truncated Average Score of High-Difficulty SQL Papers (Difficult)

**Description**: The operations team at Niuke wants to see the score situation of users on high-difficulty SQL papers.

Please help her calculate the truncated average score (the average after removing one maximum and one minimum score) of all users who completed high-difficulty SQL papers from the `exam_record` data table.

Sample data: `examination_info` (`exam_id` Paper ID, `tag` Paper Category, `difficulty` Paper Difficulty, `duration` Exam Duration, `release_time` Release Time)

| id  | exam_id | tag       | difficulty | duration | release_time        |
| --- | ------- | --------- | ---------- | -------- | ------------------- |
| 1   | 9001    | SQL       | hard       | 60       | 2020-01-01 10:00:00 |
| 2   | 9002    | Algorithm | medium     | 80       | 2020-08-02 10:00:00 |

Sample data: `exam_record` (`uid` User ID, `exam_id` Paper ID, `start_time` Start Time, `submit_time` Submit Time, `score` Score)

| id  | uid  | exam_id | start_time          | submit_time         | score  |
| --- | ---- | ------- | ------------------- | ------------------- | ------ |
| 1   | 1001 | 9001    | 2020-01-02 09:01:01 | 2020-01-02 09:21:01 | 80     |
| 2   | 1001 | 9001    | 2021-05-02 10:01:01 | 2021-05-02 10:30:01 | 81     |
| 3   | 1001 | 9001    | 2021-06-02 19:01:01 | 2021-06-02 19:31:01 | 84     |
| 4   | 1001 | 9002    | 2021-09-05 19:01:01 | 2021-09-05 19:40:01 | 89     |
| 5   | 1001 | 9001    | 2021-09-02 12:01:01 | (NULL)              | (NULL) |
| 6   | 1001 | 9002    | 2021-09-01 12:01:01 | (NULL)              | (NULL) |
| 7   | 1002 | 9002    | 2021-02-02 19:01:01 | 2021-02-02 19:30:01 | 87     |
| 8   | 1002 | 9001    | 2021-05-05 18:01:01 | 2021-05-05 18:59:02 | 90     |
| 9   | 1003 | 9001    | 2021-09-07 12:01:01 | 2021-09-07 10:31:01 | 50     |
| 10  | 1004 | 9001    | 2021-09-06 10:01:01 | (NULL)              | (NULL) |

Based on your input, your query result is as follows:

| tag | difficulty | clip_avg_score |
| --- | ---------- | -------------- |
| SQL | hard       | 81.7           |

From the `examination_info` table, it can be seen that paper 9001 is a high-difficulty SQL paper, and the scores for this paper are [80, 81, 84, 90, 50]. After removing the highest and lowest scores, we have [80, 81, 84], and the average score is 81.6666667, which rounds to 81.7 when keeping one decimal place.

**Input Description:**

The input data must contain at least 3 valid scores.

**Approach 1:** To find high-difficulty SQL papers, you need to join the `examination_info` table and find the high-difficulty courses. From `examination_info`, we know that the exam_id for high-difficulty SQL is 900
