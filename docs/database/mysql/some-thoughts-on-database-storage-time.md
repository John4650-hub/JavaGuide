---
title: MySQL Date Type Selection Recommendations
category: Database
tag:
  - MySQL
head:
  -   - meta
      - name: keywords
        content: MySQL date type selection, MySQL time storage best practices, MySQL time storage efficiency, MySQL DATETIME and TIMESTAMP differences, MySQL timestamp storage, MySQL database time storage types, MySQL development date recommendations, MySQL disadvantages of storing dates as strings, MySQL timezone settings, MySQL date range comparison, high-performance MySQL date storage, MySQL UNIX_TIMESTAMP usage, advantages and disadvantages of numeric timestamps, MySQL time storage performance optimization, MySQL TIMESTAMP timezone conversion, MySQL time format conversion, MySQL time storage space comparison, MySQL time type selection recommendations, MySQL date type performance analysis, database time storage optimization
---

In daily software development work, storing time is a fundamental and common requirement. Whether it's recording the operation time of data, the occurrence time of financial transactions, the departure time of a trip, or the order time of a user, time information is closely related to our business logic and system functionality. Therefore, correctly choosing and using MySQL's date and time types is crucial, as the appropriateness of this choice can significantly impact the accuracy of the business and the stability of the system.

This article aims to help developers re-examine and gain a deeper understanding of the different time storage methods in MySQL, so they can make more suitable choices for their project business scenarios.

## Do Not Use Strings to Store Dates

Like many beginners in databases, I also tried to use strings (such as VARCHAR) to store dates and times during my early learning phase, and I even thought this was a simple and intuitive method. After all, a format like 'YYYY-MM-DD HH:MM:SS' looks clear and understandable.

However, this is an incorrect approach, mainly due to the following two issues:

1. **Space Efficiency**: Compared to MySQL's built-in date and time types, strings typically require more storage space to represent the same time information.
1. **Inefficient Query and Calculation**:
   - **Complex and Inefficient Comparison Operations**: Date comparisons based on strings need to be done character by character in dictionary order, which is not intuitive (for example, '2024-05-01' is less than '2024-1-10') and is far less efficient than using native date and time types for numerical or timestamp comparisons.
   - **Limited Calculation Functions**: You cannot directly utilize the rich date and time functions provided by the database for calculations (such as calculating the interval between two dates or performing addition and subtraction on dates), requiring format conversion first, which adds complexity.
   - **Poor Index Performance**: String-based indexes are usually less efficient and flexible than native date and time type indexes when handling range queries (such as finding data within a specific time period).

## Choosing Between DATETIME and TIMESTAMP

`DATETIME` and `TIMESTAMP` are two very commonly used data types in MySQL for storing date and time information. Both can store time values accurate to the second (MySQL 5.6.4+ supports higher precision fractional seconds). So, how should we choose between the two in practical applications?

Let's compare them across several key dimensions:

### Time Zone Information

The `DATETIME` type stores **literal date and time values** and does **not contain any time zone information**. When you insert a `DATETIME` value, MySQL stores the exact time you provided without performing any time zone conversion.

**What problems can this cause?** If your application needs to support multiple time zones, or if the time zones of the server and client may change, then when using `DATETIME`, the application must handle time zone conversion and interpretation on its own. If not handled properly (for example, assuming all stored times belong to the same time zone while the actual environment changes), it may lead to confusion in time display or calculations.

**`TIMESTAMP` is related to time zones**. When storing, MySQL converts the time value from the current session's time zone to UTC (Coordinated Universal Time) for internal storage. When querying a `TIMESTAMP` field, MySQL converts the stored UTC time back to the time zone set for the current session for display.

This means that for the same record's `TIMESTAMP` field, querying under different session time zone settings may yield different local time representations, but they all correspond to the same absolute time point (UTC time). This is very useful for applications that require globalization and multi-time zone support.

Let's demonstrate this practically!

Table creation SQL statement:

```sql
CREATE TABLE `time_zone_test` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `date_time` datetime DEFAULT NULL,
  `time_stamp` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

Insert a
