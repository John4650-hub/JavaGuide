I don't...
Title: MySQL Stealth Conversion Causes Index Failure
Category: Database
Tag:

- MySQL
- Performance Optimization
  I don't...

> The MySQL version used in this test is `5.7.26`. As certain features of MySQL version may be changed, this does not represent the views and conclusions presented in MySQL as accurate in all MySQL versions, and the differences in versions are self-screened.

Original language: <https://www.guitu18.com/post/2019/11/24/61.html>

Foreword

The optimization of the database is a long-term task, and in order to do so, it is necessary to understand in depth the characteristics of the database. In the development process, we often encounter a number of simple but serious complications, which are often not easily located and which end up with a small oversight or a lack of understanding of a particular technical characteristic.

At the database level, the most common fear is that the index has lapsed and is not easily discovered at first because of the small amount of data. However, as the volume of outreach data rises, the performance problem becomes apparent slowly, and delays in processing can easily result in a snowball effect, eventually leading to the death or even paralysis of the database. There may be a number of reasons for the index's failure, and there are already too many relevant technical blogs, and today I would like to record **the index's failure due to the implicit conversion.**

# Data Ready

First, the storage process is used to generate 10 million test data.
A total of seven fields (including primary keys) were created in the test form, and `num1` and `num2` were saved in the same order as `ID`, of which `num2` was a string type.
`type1` and `type2` are all primary key-based simulations of 5 to simulate data of a type similar to type `type1`, which is commonly used in practical applications, but `type2` is not indexed.
`str1` and `str2` are both random strings of 20-bit lengths, `str1` cannot be `NULL`, `str2` is allowed to be `NULL`, and I would also produce a small `NULL` value (one `NULL` value per 100 data) in the `str2` field when the corresponding test data are generated.

```sql
-- Creating Test Data Sheets
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
-- Create storage process
DROP PROCEDURE IF EXISTS pre_test1;
DELIMITER //
CREATE PROCEDURE `pre_test1`()
BEGIN
DECLARE i INT DEFAULT 0;
SET autocommit = 0;
WHILE i < 10000000 DO
SET i = i + 1;
SET @str1 = SUBSTRING(MD5(RAND()), 1, 20);
-- 1 Null value per 100 data for str2
IF i % 100 = 0 THEN
SET @str2 = NULL;
ELSE
SET @str2 = @str1;
END IF;
INSERT INTO test1 (`id`, `num1`, `num2`,
`type1`, `type2`, `str1`, `str2`)
VALUES (i, i), CONCAT(i), CONCAT(i), i % 5, i % 5, @str1, @str2);
-- Optimization of services, one transaction per 10,000 data
IF i % 10000 = 0 THEN
COMMIT;
END IF;
END WHILE;
END;
// DELIMITER;
-- Execute storage process
CALL pre_test1();
```

The larger amount of data also involves the use of `MD5` to generate random strings, so it is a little slow, a little restless, and patient.

Ten million pieces of data took me 33 minutes to run. Here are some of the data generated, which is about this long.

(https://oss.javaguide.cn/github/javaguide/mysqlindex-invalidation-caused-by-implicit-conversion-01.png)

# SQL Test

First, look at this group of SQLs, four in all. Our test data sheet `num1`
