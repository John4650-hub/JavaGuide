I don't...
Title: Summary of common SQL interviews (2)
Category: Database
Tag:

- Database base
- SQL.
  I don't...

> The title comes from: [Cowside - SQL Progress Challenge](https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=240)

# Add & Delete Operation

SQL inserts records in summary:

- **General insertion (full field)**: `INSERT INTO table_name VALUES (value1, value2, ...)`
- **General insertion (limited fields)**: `INSERT INTO table_name (column1, column2,...) VALUES (value1, value2,...)`
- **Multiple one-off insertion**: `INSERT INTO table_name (column1, column2, ...) VALUES (value1_1, value1_2, ...), (value2_1, value2_2, ...)`
- **Imported from another table**: `INSERT INTO table_name SELECT * FROM table_name2 WHERE Key=value`
- **Insert with an update**: `REPLACE INTO table_name VALUES (value1, value2, ...)` (note that this principle is detected as primary or unique indexing keys are repeated and reinserted after deletion of the original record)

## Insert Record (i)

**Description**: The cowback desk will record the responses of each user to the `exam_record` table, and now two users will record the details of the responses as follows:

- User 1001 began the examination at 10:11 p.m. on 1 September 2021, 12 seconds, 90 minutes after 50 minutes;
- User 1002 started the response papers at 7:01 a.m. on 4 September 2021, in 2 seconds, and left the platform in 10 minutes.

In the response form `exam_record`, the table has been constructed as follows. Please insert the two records in the table in a single statement.

Field & Type
|\--------------------------------------------------------------------------------------------------------------------------------------------
| id | int(11) | NO | auto_increment |
| uid | int(11) | NO | (NULL) | user ID |
| exam_id | int(11) | NO | (NULL) |
| start_time | datetime | YES | (NULL) | time of submission |
| submit_time | datetime | YES | (NULL) | time of submission |
| score | tinyint(4) | YES | (NULL) |

**Answers**

```sql
-- There is a self-enhancement primary key, no manual value required
INSERT INTO exam_record (uid, exam_id, start_time, submit_time, score) VALUES
(1001, 9001, '2021-09-01 22:12', '2021-09-01 23:01:12', 90),
(1002, 9002, '2021-09-04 07:01:02', NULL, NULL);
```

## Insert Record (ii)

**Description**: There is an answer form `exam_record`, structured as follows, which includes user responses over the years, which need to be streamlined and backed up with historical data, given the increasing number of data and the increasing difficulty of maintenance.

Table `exam_record`:

Field & Type
|\--------------------------------------------------------------------------------------------------------------------------------------------
| id | int(11) | NO | auto_increment |
| uid | int(11) | NO | (NULL) | user ID |
| exam_id | int(11) | NO | (NULL) |
| start_time | datetime | YES | (NULL) | time of submission |
| submit_time | datetime | YES | (NULL) | time of submission |
| score | tinyint(4) | YES | (NULL) |

We have created a new table `exam_record_before_2021` to back up the pre-2021 trial response records, the structure and the `exam_record` table. Please import the pre-2021 completed test response records into that table.

**Answers**

```sql
INSERT INTO exam_record_before_2021 (uid, exam_id, start_time, submit_time, score)
SELECT uid, exam_id, start_time, submit_time, score
FROM exam_record
WHERE YEAR(submit_time) < 2021;
```

## Insert Record (iii)

**Description**: There is now a very difficult set of IDs for 903 SQL papers for an hour and a half, so please insert 2021-01-01 00:00 to the test information sheet `examination_info` for the release time, regardless of whether the ID paper exists, and try to insert it.

Test information sheet `examination_info`:

Field & Type
|\--------------------------------------------------------------------------------------------------------------------------------------------
| id | int(11) | NO | auto_increment |
| exam_id | int(11) | NO | (NULL) |
| tag | varchar(32) | YES | (NULL) | class label |
| difficulty | varchar
