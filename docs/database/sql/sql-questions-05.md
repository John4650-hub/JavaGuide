I don't...
Title: Summary of common SQL interviews (5)\
Category: Database\
Tag:

- Database base
- SQL.\
  I don't...

> The title comes from: [Cowside - SQL Progress Challenge](https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=240)

Harder or more difficult topics can be determined on their own merits and interview needs.

# Empty value processing

## Statistics of uncompleted volumes and uncompleted rates

**Description**:

`exam_record` (`uid` user ID, `exam_id` test ID, `start_time`, `submit_time` time, `score` score)

I'm sorry, I'm sorry.

| -   | -    | -    | -                   | -                   | -   | -   | -   |
| --- | ---- | ---- | ------------------- | ------------------- | --- | --- | --- |
| 1   | 1001 | 9001 | 2020-01-02 09:01    | 2020-01-02 09:01    | 80  |     |     |
| 2   | 1001 | 9001 | 2021-05-02 10:01:01 | 2021-05-02 10:30:01 | 81  |     |     |
| 3   | 1001 | 9001 | 2021-09-02 12:01:01 | (NULL)              |     |     |     |

Please measure the number of uncompleted files with incomplete status `incomplete_cnt` and the rate of uncompleted `incomplete_rate`. The results from the sample data are as follows:

| -    | -   | -     | -   |
| ---- | --- | ----- | --- |
| 9001 | 1   | 0.333 |     |

Explanation: Pilot volume 9001 recorded 3 responses, of which 2 were completed and 1 was not completed, resulting in 1 outstanding and 0.333 (reserved 3 decimals).

**Thinking**

This question requires only that one of the conditions be conditional, one of which is unconditional; either the conditions are checked separately and then the conditions are merged; or the conditions are judged directly within self.

**Answers**

Writing 1:

```sql
SELECT uid,
       COUNT(submit_time IS NULL OR NULL) AS incomplete_cnt,
       ROUND(COUNT(submit_time IS NULL OR NULL) / COUNT(*), 3) AS complete_rate
FROM exam_record
GROUP BY exam_id
HAVING incomplete_cnt < 0;
```

Writing 2:

```sql
SELECT uid,
       COUNT(submit_time IS NULL OR NULL) AS incomplete_cnt,
       ROUND(COUNT(submit_time IS NULL OR NULL) / COUNT(*), 3) AS complete_rate
FROM exam_record
GROUP BY exam_id
HAVING incomplete_cnt < 0;
```

Both formulations are possible, except in the middle, where one is `COUNT`, and the other is directly above `IF`, which is more intuitive, and finally the `HAVING` explains whether it is `complete_rate` or `incomplete_cnt`, if it is not `0`, or not `0`.

## 0 average time and average score for the high-probability test

**Description**:

Current user information sheet `user_info` (`uid` user ID, `nick_name` nickname, `achievement`, `level`, `job` occupational orientation, `register_time` registration time)

I mean, I've got a lot of work to do.

| -   | -    | -      | -    | -   | -         | -             | -   |
| --- | ---- | ------ | ---- | --- | --------- | ------------- | --- |
| 1   | 1001 | Cow 1  | 10   | 0   | Algoritha | 2020-01 0:00  |     |
| 2   | 1002 | Bull 2 | 2100 | 6   | Algoritha | 2020-01 10:00 |     |

`examining_info` (`exam_id` test ID, `tag` test category, `difficulty` test difficulty, `duration` examination length, `release_time` release time) with data as follows:

I'm sorry.

| -   | -   | -         | -   | -                |
| --- | --- | --------- | --- | ---------------- |
| 1   | 901 | SQL       | 60  | 2020-01-01 10:00 |
| 2   | 902 | SQL       | 60  | 2020-01-10:00    |
| 3   | 904 | algorithm | 80  | 2020-01-01 10:00 |

`exam_record` (`uid` user ID, `exam_id` test ID, `start_time` start time, `submit_time` time, `score` score)

I'm sorry, I'm sorry.\
| \- | - | - | -
