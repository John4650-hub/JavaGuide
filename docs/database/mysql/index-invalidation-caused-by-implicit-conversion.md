---
title: MySQL Implicit Conversion Causing Index Invalidity
category: Database
tag:
  - MySQL
  - Performance Optimization
---

> The MySQL version used for this test is `5.7.26`. As MySQL versions are updated, certain features may change. This article does not represent that the views and conclusions stated are accurate for all versions of MySQL; please verify version differences on your own.
>
> Original article: <https://www.guitu18.com/post/2019/11/24/61.html>

## Introduction

Database optimization is a long-term and challenging task. To achieve optimization, one must deeply understand various features of the database. During development, we often encounter seemingly simple issues that lead to serious consequences. These problems are often difficult to locate, requiring time and effort to troubleshoot, only to find that they stem from a small oversight or a lack of understanding of a specific technical feature.

At the database level, one of the most common issues is index invalidation, which may not be easily detected at first due to the small amount of data. However, as the business expands and the data volume increases, performance issues gradually become apparent. If not addressed in a timely manner, it can easily lead to a snowball effect, ultimately causing the database to freeze or even crash. There are many potential causes for index invalidation, and numerous technical blogs have covered this topic. Today, I want to document **index invalidation caused by implicit conversion**.

## Data Preparation

First, we will use a stored procedure to generate 10 million test data entries. The test table consists of 7 fields (including the primary key). `num1` and `num2` store sequential numbers identical to `ID`, with `num2` being of string type. `type1` and `type2` both store the modulus of the primary key with 5, simulating commonly used type-like data in actual applications, but `type2` does not have an index. `str1` and `str2` both store a random string of 20 characters in length, with `str1` not allowed to be `NULL` and `str2` allowed to be `NULL`. Accordingly, when generating test data, I will also produce a small number of `NULL` values in the `str2` field (one `NULL` value for every 100 entries).

```sql
-- Create test data table
DROP TABLE IF EXISTS test1;
CREATE TABLE `test1` (
    `id` int(11) NOT NULL,
    `num1` int(11) NOT NULL DEFAULT '0',
    `num2` varchar(11) NOT NULL DEFAULT '',
    `type1` int(4) NOT NULL DEFAULT '0',
    `type2` int(4) NOT NULL DEFAULT '0',
    `str1` varchar(100) NOT NULL DEFAULT '',
    `str2` varchar(100) DEFAULT NULL,
    PRIMARY KEY (`id`),
    KEY `num1` (`num1`),
    KEY `num2` (`num2`),
    KEY `type1` (`type1`),
    KEY `str1` (`str1`),
    KEY `str2` (`str2`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
-- Create stored procedure
DROP PROCEDURE IF EXISTS pre_test1;
DELIMITER //
CREATE PROCEDURE `pre_test1`()
BEGIN
    DECLARE i INT DEFAULT 0;
    SET autocommit = 0;
    WHILE i < 10000000 DO
        SET i = i + 1;
        SET @str1 = SUBSTRING(MD5(RAND()),1,20);
        -- Generate a null value for str2 every 100 entries
        IF i % 100 = 0 THEN
            SET @str2 = NULL;
        ELSE
            SET @str2 = @str1;
        END IF;
        INSERT INTO test1 (`id`, `num1`, `num2`,
        `type1`, `type2`, `str1`, `str2`)
        VALUES (CONCAT('', i), CONCAT('', i),
        CONCAT('', i), i%5, i%5, @str1, @str2);
        -- Transaction optimization, commit every 10,000 entries
        IF i % 10000 = 0 THEN
            COMMIT;
        END IF;
    END WHILE;
END;
// DELIMITER ;
-- Execute stored procedure
CALL pre_test1();
```

The data volume is quite large, and since it involves using `MD5` to generate random strings, the speed is a bit slow. Please be patient and wait.

It took me 33 minutes to generate 10 million entries (the actual time depends on your computer's hardware configuration). Here are a few examples of the generated data, which look like this.

!\[\](https://oss.javaguide.cn/github/javaguide
