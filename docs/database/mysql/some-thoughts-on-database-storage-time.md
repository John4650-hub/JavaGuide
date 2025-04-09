I don't...
title: MySQL Date Type Selection
Category: Database
Tag:

- MySQL
  Head:
- Meta
- Name: keywords
  MySQL date-type selection, MySQL time-storage best practice, MySQL time-storage efficiency, MySQL DATETIME and TIMESTAMP difference, MySQL timestamp storage, MySQL timestamp storage type, MySQL development date recommendation, MySQL string storage date disadvantages, MySQL time-space setting method, MySQL date range comparison, High performance MySQL date storage, MySQL UNIX_TIMESTAMP use method, Numeric Time-plugging, MySQL time-storage optimization, MySQL time-storage conversion, MySQL time-storage format conversion, MySQL time-storage space comparison, MySQL choice of time type, MySQL date-type analysis, MySQL date-type analysis
  I don't...

Storage time is a basic and common requirement in day-to-day software development. Whether it is the time of operation to record data, the time of the financial transaction, the time of departure of the journey, the time of the user's next order, etc., time information is closely related to our business logic and system functions. Therefore, the correct choice and use of the date-time type of MySQL is crucial, and its appropriateness may even have a significant impact on the accuracy of operations and the stability of systems.

This paper is intended to help developers re-examine and understand in depth the different time storage methods in MySQL in order to make a more suitable choice for project business scenarios.

# Do not store the date with a string

Like many database beginners, at an early learning stage, writers have tried to store dates and times using string types (e.g. VARCHAR) even as a simple intuitive approach. After all, 'YYYY-MM-DD HH:MM:SS' looks so clear and comprehensible.

However, this is an incorrect approach, and there are two main problems:

1. **Space efficiency**: The string usually requires more storage space to denote the same time information than the date-type of the MySQL built-in.
1. **Inefficiency of queries and calculations**:
   - **Comparatively complex and inefficient**: Date comparisons based on strings need to be character-by-character, not only intuitive (e.g. '2024-05-01' is less than '2024-1-10'), but also far less efficient than numerical or point-to-point comparisons using date-of-birth type.
   - **The computational function is limited**: It is not possible to operate directly from the rich date-time functions provided by the database (e.g. to calculate the interval between two dates, to add or subtract the date, etc.), which requires a first conversion of the format and adds complexity.
   - **Poor index performance**: String-based indexes tend to be less efficient and more flexible than indices of the original date-time type when dealing with scope queries (e.g., finding data within a given time period).

# DATETIME and TIMESTAMP Selection

`DATETIME` and `TIMESTAMP` are two of MySQL's very common data types used to store date and time information. They can store the time value of the time to the second (MySQL 5.6.4+ supports small seconds for higher accuracy). How, then, should we choose between them in practical application?

We compare them from several critical dimensions:

Time zone information

The `DATETIME` type is stored as the date and time value and it itself does not contain any time zone information. When you insert a `DATETIME` value, MySQL stores the exact time you provide, without any time zone conversion.

**What's wrong with that?** If your application needs to support multiple time zones, or if the time zone of the server or client is subject to change, the application needs to process the conversion and interpretation of the time zone when using `DATETIME`. If mishandled (assuming, for example, that all storage time falls within the same time zone, but the actual environment changes), it may lead to confusion in time display or calculation.

**`TIMESTAMP` relates to time zones.** When you store, MySQL converts the time value from the current session time zone to UTC for internal storage. When querying the `TIMESTAMP` field, MySQL converts stored UTC time back to the time zone set by the current session.

This means that for the `TIMESTAMP` fields recorded in the same article, searching in different session time zones may be seen at different local times, but they all correspond to the same absolute time point (UTC time). This is very useful for applications that require globalization and multi-temporal support.

Here's a real demonstration!

Form SQL statement:

```sql
CREATE TABLE `time_zone_test` (
`id` BIGINT(20) NOT NULL AUTO_INCREMENT,
`date_time` DATETIME DEFAULT NULL,
`timestamp` TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

Insert data (assuming that the
