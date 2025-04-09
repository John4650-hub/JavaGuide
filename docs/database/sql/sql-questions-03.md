I don't...
Title: Summary of common SQL interviews (3)\
Category: Database\
Tag:

- Database base
- SQL.\
  I don't...

> The title comes from: [Cowside - SQL Progress Challenge](https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=240)

Harder or more difficult topics can be determined on their own merits and interview needs.

# Aggregation function

## Cut-off average for the SQL category of hard-to-end test paper scores (more difficult)

**Description**: Cowboys' business students would like to see your scores in the SQL category for the difficult papers.

Please help her calculate the cut-off average for all users of the SQL category's high-cost test scores from the `exam_record` data sheet (with one maximum and one minimum average removed).

Example data: `examination_info` (`exam_id` test ID, `tag` test category, `difficulty` test difficulty, `during` examination length, `release_time` release time)

## I'm sorry.

1 | 901 | SQL | 60 | 2020-01-01 10:00 |\
| 2 | 902 | Algorithm | 80 | 2020-08-02 10:00 |

Example data: `exam_record` (uid user ID, exam_id test ID, start_time start time, submit_time time, score score)

I'm sorry, I'm sorry.\
| - | - | - | - | - | - | - | - | | | | | | | | | | | | | | | | | |\
1 | 1001 | 9001 | 2020-01-02 09:01 | 2020-01-02 09:01 | 80 |\
2 | 1001 | 9001 | 2021-05-02 10:01:01 | 2021-05-02 10:30:01 | 81 |\
| 3 | 1001 | 9001 | 2021-06-02 19:01 | 2021-06-02 19:31:01 | 84 |\
| 4 | 1001 | 9002 | 2021-09-05 01:01 | 2021-09-05 40:01 | 89 |\
| 5 | 1001 | 9001 | 2021-09-02 12:01:01 | (NULL) |\
| 6 | 1001 | 9002 | 2021-09-01 12:01:01 | (NULL) |\
7 | 1002 | 9002 | 2021-02-02 19:01:01 | 2021-02-02 19:30:01 | 87 |\
8 | 1002 | 9001 | 2021-05-05 18:01:01 | 2021-05-05 18:59:02 | 90 |\
9 | 1003 | 9001 | 2021-09-07 12:01:01 | 2021-09-07 10:31:01 | 50 |\
| 1004 | 9001 | 2021-09-06 01:01 | (NULL) |

According to the results of your query entered:

It's not like I'm going to be able to do this.\
| - | - | - | - |\
SQL Hard | 81.7 |

As can be seen from the `examination_info` table, volume 9001 is a difficult SQL trial, with [80, 81, 84, 90, 50], with [80, 81, 84], with an average of 81.666667, with 81.7 after one decimal.

**Enter description:**

At least 3 valid fractions of input data

**Conception I:** In order to find a difficult SQL test, it will certainly be necessary to link this table to the `examination_info`, and then to find out the difficult course, as known by the `examination_info`, the exam_id of the difficult SQL is 9001, so then the search is conditional on exam_id = 9001;

`SELECT * FROM exam_record WHERE exam_id = 9001`

Then find the highest score from `exam_record` where exam_id = 9001;

Then find the lowest score from `exam_record` where exam_id = 9001;

In the query result set, remove the highest and lowest points, the most intuitive is the NOT IN or the NOT EXISTS, which is done here with NOT IN.

\`SELECT tag, difficulty, ROUND(AVG(score), 1) AS clip_avg_score FROM export_info INNER JOIN
