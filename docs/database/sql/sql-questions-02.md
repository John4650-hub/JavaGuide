---
title: Summary of Common SQL Interview Questions (2)
category: Database
tag:
  - Database Basics
  - SQL
---

> Source of the question: [Niuke Problem Boss - Advanced SQL Challenge](https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=240)

## Insert, Delete, and Update Operations

Summary of SQL methods for inserting records:

- **Normal Insert (All Fields)**: `INSERT INTO table_name VALUES (value1, value2, ...)`
- **Normal Insert (Specified Fields)**: `INSERT INTO table_name (column1, column2, ...) VALUES (value1, value2, ...)`
- **Insert Multiple Records at Once**: `INSERT INTO table_name (column1, column2, ...) VALUES (value1_1, value1_2, ...), (value2_1, value2_2, ...), ...`
- **Import from Another Table**: `INSERT INTO table_name SELECT * FROM table_name2 [WHERE key=value]`
- **Insert with Update**: `REPLACE INTO table_name VALUES (value1, value2, ...)` (Note that this method deletes the original record if a primary key or unique index key duplicate is detected, then reinserts it)

### Inserting Records (1)

**Description**: The Niuke backend records each user's exam record in the `exam_record` table. Two users' exam records are as follows:

- User 1001 started the exam 9001 at 10:11:12 PM on September 1, 2021, and submitted it after 50 minutes, scoring 90 points;
- User 1002 started the exam 9002 at 7:01:02 AM on September 4, 2021, and left the platform after 10 minutes.

The structure of the `exam_record` table is already established as follows; please use a single statement to insert these two records into the table.

| Field       | Type       | Null | Key | Extra          | Default | Comment           |
| ----------- | ---------- | ---- | --- | -------------- | ------- | ----------------- |
| id          | int(11)    | NO   | PRI | auto_increment | (NULL)  | Auto-increment ID |
| uid         | int(11)    | NO   |     |                | (NULL)  | User ID           |
| exam_id     | int(11)    | NO   |     |                | (NULL)  | Exam ID           |
| start_time  | datetime   | NO   |     |                | (NULL)  | Start Time        |
| submit_time | datetime   | YES  |     |                | (NULL)  | Submit Time       |
| score       | tinyint(4) | YES  |     |                | (NULL)  | Score             |

**Answer**:

```sql
-- Auto-incrementing primary key, no manual assignment required
INSERT INTO exam_record (uid, exam_id, start_time, submit_time, score) VALUES
(1001, 9001, '2021-09-01 22:11:12', '2021-09-01 23:01:12', 90),
(1002, 9002, '2021-09-04 07:01:02', NULL, NULL);
```

### Inserting Records (2)

**Description**: There is an `exam_record` table containing user exam records for many years. Due to the increasing amount of data, maintenance difficulty is getting higher, and the content of the data table needs to be simplified, with historical data backed up.

Table `exam_record`:

| Field       | Type       | Null | Key | Extra          | Default | Comment           |
| ----------- | ---------- | ---- | --- | -------------- | ------- | ----------------- |
| id          | int(11)    | NO   | PRI | auto_increment | (NULL)  | Auto-increment ID |
| uid         | int(11)    | NO   |     |                | (NULL)  | User ID           |
| exam_id     | int(11)    | NO   |     |                | (NULL)  | Exam ID           |
| start_time  | datetime   | NO   |     |                | (NULL)  | Start Time        |
| submit_time | datetime   | YES  |     |                | (NULL)  | Submit Time       |
| score       | tinyint(4) | YES  |     |                | (NULL)  | Score             |

We have created a new table `exam_record_before_2021` to back up exam records before 2021, which has the same structure as the `exam_record` table. Please import the completed exam records before 2021 into this new table.

**Answer**:

```sql
INSERT INTO exam_record_before_2021 (uid, exam_id, start_time, submit_time, score)
SELECT uid, exam_id, start_time, submit_time, score
FROM exam_record
WHERE YEAR(submit_time) < 2021;
```

### Inserting Records (3)

**Description**: There is a difficult SQL exam with ID 9003, which lasts for one and a half hours. Please insert the release time `2021-01-01 00:00:00` into the `examination_info` table, regardless of whether this ID exam exists or not.

Table `examination_info`:

| Field        | Type        | Null | Key | Extra          | Default | Comment            |
| ------------ | ----------- | ---- | --- | -------------- | ------- | ------------------ |
| id           | int(11)     | NO   | PRI | auto_increment | (NULL)  | Auto-increment ID  |
| exam_id      | int(11)     | NO   | UNI |                | (NULL)  | Exam ID            |
| tag          | varchar(32) | YES  |     |                | (NULL)  | Category Tag       |
| difficulty   | varchar(8)  | YES  |     |                | (NULL)  | Difficulty         |
| duration     | int(11)     | NO   |     |                | (NULL)  | Duration (minutes) |
| release_time | datetime    | YES  |     |                | (NULL)  | Release Time       |

**Answer**:

```sql
REPLACE INTO examination_info VALUES
 (NULL, 9003, "SQL", "hard", 90, "2021-01-01 00:00:00");
```

### Updating Records (1)

**Description**: There is an `examination_info` table. The table structure is as follows:

| Field        | Type     | Null | Key | Extra          | Default | Comment           |
| ------------ | -------- | ---- | --- | -------------- | ------- | ----------------- |
| id           | int(11)  | NO   | PRI | auto_increment | (NULL)  | Auto-increment ID |
| exam_id      | int(11)  | NO   | UNI |                | (NULL)  | Exam ID           |
| tag          | char(32) | YES  |     |                | (NULL)  | Category Tag      |
| difficulty   | char(8)  | YES  |     |                | (NULL)  | Difficulty        |
| duration     | int(11)  | NO   |     |                | (NULL)  | Duration          |
| release_time | datetime | YES  |     |                | (NULL)  | Release Time      |

Please change all `tag` fields of `tag` equal to `PYTHON` in the `examination_info` table to `Python`.

**Thought Process**: There are two ways to solve this problem. The most straightforward approach is to directly use `update + where` to specify the conditional update. The second method is to find and replace based on the field that needs to be modified.

**Answer 1**:

```sql
UPDATE examination_info SET tag = 'Python' WHERE tag='PYTHON'
```

**Answer 2**:

```sql
UPDATE examination_info
SET tag = REPLACE(tag,'PYTHON','Python')

# REPLACE (Target Field, "Search Content", "Replacement Content")
```

### Updating Records (2)

**Description**: There is an `exam_record` table, which contains years of user exam records. The table structure is as follows: The **`submit_time`** is the completion time (Note this statement).

| Field       | Type       | Null | Key | Extra          | Default | Comment           |
| ----------- | ---------- | ---- | --- | -------------- | ------- | ----------------- |
| id          | int(11)    | NO   | PRI | auto_increment | (NULL)  | Auto-increment ID |
| uid         | int(11)    | NO   |     |                | (NULL)  | User ID           |
| exam_id     | int(11)    | NO   |     |                | (NULL)  | Exam ID           |
| start_time  | datetime   | NO   |     |                | (NULL)  | Start Time        |
| submit_time | datetime   | YES  |     |                | (NULL)  | Submit Time       |
| score       | tinyint(4) | YES  |     |                | (NULL)  | Score             |

**Requirement**: Please change all uncompleted records in the `exam_record` table that started before September 1, 2021, to be marked as completed. Specifically: Set the completion time to '2099-01-01 00:00:00' and the score to 0.

**Thought Process**: Pay attention to the key phrases in the problem statement (highlighted) regarding "xxx time" before this condition. This suggests that we need to perform a time comparison using either `xxx_time < "2021-09-01 00:00:00"` or using the `date()` function for comparison. The second condition is "uncompleted", meaning the submission time is NULL; hence, `submit_time IS NULL`.

**Answer**:

```sql
UPDATE exam_record SET submit_time = '2099-01-01 00:00:00', score = 0 WHERE DATE(start_time) < "2021-09-01" AND submit_time IS null
```

### Deleting Records (1)

**Description**: There is an `exam_record` table containing many years of user records as follows:

The exam record table `exam_record`: **`start_time`** is the start time and `submit_time` is the submission/end time.

| Field       | Type       | Null | Key | Extra          | Default | Comment           |
| ----------- | ---------- | ---- | --- | -------------- | ------- | ----------------- |
| id          | int(11)    | NO   | PRI | auto_increment | (NULL)  | Auto-increment ID |
| uid         | int(11)    | NO   |     |                | (NULL)  | User ID           |
| exam_id     | int(11)    | NO   |     |                | (NULL)  | Exam ID           |
| start_time  | datetime   | NO   |     |                | (NULL)  | Start Time        |
| submit_time | datetime   | YES  |     |                | (NULL)  | Submit Time       |
| score       | tinyint(4) | YES  |     |                | (NULL)  | Score             |

**Requirement**: Delete records in the `exam_record` table where the exam duration is less than 5 minutes and the score is failing (the passing score is 60).

**Thought Process**: While this question practices deletion, it exactly tests the usage of time functions. Two common functions for minute comparison are **`TIMEDIFF`** and **`TIMESTAMPDIFF`**, both with slightly different usages, with the latter being more flexible.

1. `TIMEDIFF`: The time difference between two times

```sql
TIMEDIFF(time1, time2)
```

Both parameters are required and must be a time or datetime expression. If either is invalid or NULL, the function returns NULL.

For this problem, it can be applied within the minute function because the TIMEDIFF result gives the time difference, and wrapping this in a MINUTE function gives the minutes.

2. `TIMESTAMPDIFF`: Used to calculate the time difference between two dates

```sql
TIMESTAMPDIFF(unit, datetime_expr1, datetime_expr2)
# Parameter description
# unit: Common optional values for the time difference returned:
SECOND: seconds
MINUTE: minutes
HOUR: hours
DAY: days
WEEK: weeks
MONTH: months
QUARTER: quarters
YEAR: years
# The TIMESTAMPDIFF function returns the result of datetime_expr2 - datetime_expr1 (in simple terms: the latter - the former, i.e., 2-1), where datetime_expr1 and datetime_expr2 can be DATE or DATETIME type values.
```

This question requires minute comparison, hence `TIMESTAMPDIFF(MINUTE, start_time, submit_time) < 5`.

**Answer**:

```sql
DELETE FROM exam_record WHERE MINUTE (TIMEDIFF(submit_time, start_time)) < 5 AND score < 60
```

```sql
DELETE FROM exam_record WHERE TIMESTAMPDIFF(MINUTE, start_time, submit_time) < 5 AND score < 60
```

### Deleting Records (2)

**Description**: There is an `exam_record` table that includes numerous user exam records, structured as follows:

Exam record table `exam_record`: `start_time` is the exam start time, and `submit_time` is the submission time. If incomplete, it is NULL.

| Field       | Type       | Null | Key | Extra          | Default | Comment           |
| ----------- | ---------- | :--: | --- | -------------- | ------- | ----------------- |
| id          | int(11)    |  NO  | PRI | auto_increment | (NULL)  | Auto-increment ID |
| uid         | int(11)    |  NO  |     |                | (NULL)  | User ID           |
| exam_id     | int(11)    |  NO  |     |                | (NULL)  | Exam ID           |
| start_time  | datetime   |  NO  |     |                | (NULL)  | Start Time        |
| submit_time | datetime   | YES  |     |                | (NULL)  | Submit Time       |
| score       | tinyint(4) | YES  |     |                | (NULL)  | Score             |

**Requirement**: Delete the earliest 3 records among those that are either uncompleted or have an exam duration of less than 5 minutes.

**Thought Process**: This problem is relatively simple, but be sure to note the information given in the prompt. The end time, if incomplete, is NULL, which means this is one condition.

Another condition is being less than 5 minutes, similar to the previous question, but here it is **or**, so either condition suffices. Lastly, this tests sorting and the use of limit.

**Answer**:

```sql
DELETE FROM exam_record WHERE submit_time IS NULL OR TIMESTAMPDIFF(MINUTE, start_time, submit_time) < 5
ORDER BY start_time
LIMIT 3
# The default is ascending, desc is descending order
```

### Deleting Records (3)

**Description**: There is an `exam_record` table containing multi-year user exam records structured as follows:

| Field       | Type       | Null | Key | Extra          | Default | Comment           |
| ----------- | ---------- | :--: | --- | -------------- | ------- | ----------------- |
| id          | int(11)    |  NO  | PRI | auto_increment | (NULL)  | Auto-increment ID |
| uid         | int(11)    |  NO  |     |                | (NULL)  | User ID           |
| exam_id     | int(11)    |  NO  |     |                | (NULL)  | Exam ID           |
| start_time  | datetime   |  NO  |     |                | (NULL)  | Start Time        |
| submit_time | datetime   | YES  |     |                | (NULL)  | Submit Time       |
| score       | tinyint(4) | YES  |     |                | (NULL)  | Score             |

**Requirement**: Delete all records in the `exam_record` table and reset the auto-incrementing primary key.

**Thought Process**: This question tests the differences between three delete statements. Note the highlighted part about resetting the primary key:

- `DROP`: Deletes the table and its structure and is irreversible.
- `TRUNCATE`: Clears the table but does not delete the structure. It is irreversible.
- `DELETE`: Deletes data and is reversible.

In this case, `TRUNCATE` is chosen because it only affects the table; `TRUNCATE` clears all rows from the table but keeps the structure, constraints, indexes, etc.; `TRUNCATE` resets the auto-increment value; after using `TRUNCATE`, the space occupied by the table and indexes reverts to the initial size.

This problem can also be approached with `DELETE`, but would require a manual `ALTER` after deleting to set the primary key initial value.

Similarly, `DROP` could be used but involves deleting the entire table, including its structure and then recreating it.

**Answer**:

```sql
TRUNCATE exam_record;
```

## Table and Index Operations

### Creating a New Table

**Description**: There is a user information table that contains user information registered on the platform over the years. As the Niuke platform continues to grow, the user base is rapidly increasing. To efficiently serve high-activity users, there is a need to split some users into a new table.

Original user information table:

| Field         | Type        | Null | Key | Default           | Extra          | Comment           |
| ------------- | ----------- | ---- | --- | ----------------- | -------------- | ----------------- |
| id            | int(11)     | NO   | PRI | (NULL)            | auto_increment | Auto-increment ID |
| uid           | int(11)     | NO   | UNI | (NULL)            |                | User ID           |
| nick_name     | varchar(64) | YES  |     | (NULL)            |                | Nickname          |
| achievement   | int(11)     | YES  |     | 0                 |                | Achievement Value |
| level         | int(11)     | YES  |     | (NULL)            |                | User Level        |
| job           | varchar(32) | YES  |     | (NULL)            |                | Career Direction  |
| register_time | datetime    | YES  |     | CURRENT_TIMESTAMP |                | Registration Time |

As a data analyst, please **create a quality user information table named user_info_vip** with the same structure as the user information table.

The output you should return should match the table structure shown below. Please write the create table statement to include all constraints and comments.

| Field         | Type        | Null | Key | Default           | Extra          | Comment           |
| ------------- | ----------- | ---- | --- | ----------------- | -------------- | ----------------- |
| id            | int(11)     | NO   | PRI | (NULL)            | auto_increment | Auto-increment ID |
| uid           | int(11)     | NO   | UNI | (NULL)            |                | User ID           |
| nick_name     | varchar(64) | YES  |     | (NULL)            |                | Nickname          |
| achievement   | int(11)     | YES  |     | 0                 |                | Achievement Value |
| level         | int(11)     | YES  |     | (NULL)            |                | User Level        |
| job           | varchar(32) | YES  |     | (NULL)            |                | Career Direction  |
| register_time | datetime    | YES  |     | CURRENT_TIMESTAMP |                | Registration Time |

**Thought Process**: If the old table name was provided, one could simply use `create table new_table as select * from old_table;` However, since the name is not given, it needs to be created manually, paying attention to defaults and key creation. This is relatively simple (Note: if executed on Niuke.com, ensure the comment matches the requirement, including case sensitivity, otherwise it will fail).

**Answer**:

```sql
CREATE TABLE IF NOT EXISTS user_info_vip(
    id INT(11) PRIMARY KEY AUTO_INCREMENT COMMENT 'Auto-increment ID',
    uid INT(11) UNIQUE NOT NULL COMMENT 'User ID',
    nick_name VARCHAR(64) COMMENT 'Nickname',
    achievement INT(11) DEFAULT 0 COMMENT 'Achievement Value',
    `level` INT(11) COMMENT 'User Level',
    job VARCHAR(32) COMMENT 'Career Direction',
    register_time DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT 'Registration Time'
) CHARACTER SET UTF8
```

### Modifying a Table

**Description**: There is a user information table `user_info` which contains user information registered on the platform over the years.

**User Information Table `user_info`:**

| Field         | Type        | Null | Key | Default           | Extra          | Comment           |
| ------------- | ----------- | ---- | --- | ----------------- | -------------- | ----------------- |
| id            | int(11)     | NO   | PRI | (NULL)            | auto_increment | Auto-increment ID |
| uid           | int(11)     | NO   | UNI | (NULL)            |                | User ID           |
| nick_name     | varchar(64) | YES  |     | (NULL)            |                | Nickname          |
| achievement   | int(11)     | YES  |     | 0                 |                | Achievement Value |
| level         | int(11)     | YES  |     | (NULL)            |                | User Level        |
| job           | varchar(32) | YES  |     | (NULL)            |                | Career Direction  |
| register_time | datetime    | YES  |     | CURRENT_TIMESTAMP |                | Registration Time |

**Requirement**: Please add a new column `school` that can store a maximum of 15 Chinese characters after the `level` column in the user information table; rename the `job` column to `profession` while also changing the `varchar` field length to 10; set the default value of `achievement` to 0.

**Thought Process**: Before answering, one should understand the basic usage of the ALTER statement:

- Add a column: `ALTER TABLE table_name ADD COLUMN column_name type [first | after column_name];` (first: add before a certain column, after is the opposite)
- Modify the column type or constraints: `ALTER TABLE table_name MODIFY COLUMN column_name new_type [new_constraints];`
- Rename the column: `ALTER TABLE table_name CHANGE COLUMN old_column_name new_column_name type;`
- Delete a column: `ALTER TABLE table_name DROP COLUMN column_name;`
- Rename a table: `ALTER TABLE table_name RENAME [TO] new_table_name;`
- Move a column to the first position: `ALTER TABLE table_name MODIFY COLUMN column_name type FIRST;`

The `COLUMN` keyword can actually be omitted, but it is listed here for clarity in format.

When making modifications, if there are multiple changes, they can be combined, but formatting is essential.

**Answer**:

```sql
ALTER TABLE user_info
    ADD school VARCHAR(15) AFTER level,
    CHANGE job profession VARCHAR(10),
    MODIFY achievement INT(11) DEFAULT 0;
```

### Deleting a Table

**Description**: There is an `exam_record` table containing numerous user exam records over the years. Every year, a backup table named `exam_record_{YEAR}` is created, where `{YEAR}` corresponds to the year.

Now, due to increasing data, storage is running low, so please delete the old backup tables from 2011 to 2014 (if they exist).

**Thought Process**: This question is straightforward; just delete them. To avoid additional complexity, you can list the tables to be deleted separated by commas in one line; one might ask: What if there are many tables to delete? No worries, scripts can be written to delete multiple tables.

**Answer**:

```sql
DROP TABLE IF EXISTS exam_record_2011;
DROP TABLE IF EXISTS exam_record_2012;
DROP TABLE IF EXISTS exam_record_2013;
DROP TABLE IF EXISTS exam_record_2014;
```

### Creating an Index

**Description**: There is an `examination_info` table containing information on various types of exams. To facilitate quicker queries on this table, the following indexes need to be created in the `examination_info` table:

The rules are as follows: create a normal index `idx_duration` on the `duration` column, create a unique index `uniq_idx_exam_id` on the `exam_id` column, and a full-text index `full_idx_tag` on the `tag` column.

Based on the question, the expected output is as follows:

| examination_info | 0   | PRIMARY          | 1   | id       | A   | 0   |     |     |     | BTREE    |
| ---------------- | --- | ---------------- | --- | -------- | --- | --- | --- | --- | --- | -------- |
| examination_info | 0   | uniq_idx_exam_id | 1   | exam_id  | A   | 0   |     |     | YES | BTREE    |
| examination_info | 1   | idx_duration     | 1   | duration | A   | 0   |     |     |     | BTREE    |
| examination_info | 1   | full_idx_tag     | 1   | tag      |     | 0   |     |     | YES | FULLTEXT |

Note: The backend will compare the output using `SHOW INDEX FROM examination_info`.

**Thought Process**: Before answering, one needs to understand common index types:

- B-Tree Index: The B-Tree (or balanced tree) index is the most common and default type. It's suitable for various query conditions and can quickly locate the data that meets the condition. It's typically used for ordinary queries supporting equality, range queries, and sorting.
- Unique Index: Similar to the ordinary B-Tree index, but it requires the indexed column’s values to be unique, which means MySQL will check for uniqueness during insertion and updates.
- Primary Key Index: This is a special type of unique index used to uniquely identify every row in a table. Each table can only have one primary key index, which helps enhance access speed and data integrity.
- Full-Text Index: A full-text index is used for full-text searches within text data. It supports keyword searches in text fields, not just simple equality or range queries. It's suitable for applications needing full-text search.

With this foundational knowledge, the answer to this question is now clear.

**Answer**:

```sql
ALTER TABLE examination_info
    ADD INDEX idx_duration(duration),
    ADD UNIQUE INDEX uniq_idx_exam_id(exam_id),
    ADD FULLTEXT INDEX full_idx_tag(tag);
```

### Deleting an Index

**Description**: Please delete the unique index `uniq_idx_exam_id` and the full-text index `full_idx_tag` on the `examination_info` table.

**Thought Process**: This question tests the basic syntax for deleting an index:

```sql
-- Using DROP INDEX to delete an index
DROP INDEX idx_name ON table_name;

-- Using ALTER TABLE to delete an index
ALTER TABLE table_name DROP INDEX idx_name;
```

It is important to note that in MySQL, multiple indexes cannot be deleted in a single operation. Each operation can only specify one index name for deletion.

Additionally, **DROP** commands should be used with caution!

**Answer**:

```sql
DROP INDEX uniq_idx_exam_id ON examination_info;
DROP INDEX full_idx_tag ON examination_info;
```

<!-- @include: @article-footer.snippet.md -->
