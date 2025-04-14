---
title: Introduction to Distributed ID & Summary of Implementation Solutions
category: Distributed
---

## Introduction to Distributed ID

### What is an ID?

In daily development, we need to uniquely represent various data in the system using IDs. For example, a user ID corresponds to and only corresponds to one person, a product ID corresponds to and only corresponds to one product, and an order ID corresponds to and only corresponds to one order.

In real life, we also have various IDs, such as an ID card ID that corresponds to and only corresponds to one person, and an address ID that corresponds to and only corresponds to one address.

In simple terms, **an ID is a unique identifier for data**.

### What is a Distributed ID?

A distributed ID is an ID used in a distributed system. Distributed IDs do not exist in real life; they are a concept within computer systems.

Let me give a simple example of sharding.

In one of our projects, we used a standalone MySQL database. However, unexpectedly, after the project went live for a month, as the number of users increased, the data volume of the entire system grew larger. The standalone MySQL could no longer support it, and we needed to implement sharding (recommended Sharding-JDBC).

After sharding, the data is distributed across databases on different servers, and the auto-incrementing primary key of the database can no longer guarantee the uniqueness of the generated primary key. **How do we generate a globally unique primary key for different data nodes?**

At this point, we need to generate **distributed IDs**.

![](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/id-after-the-sub-table-not-conflict.png)

### What Requirements Must Distributed IDs Meet?

![](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/distributed-id-requirements.png)

As an essential part of a distributed system, distributed IDs are needed in many places.

A basic distributed ID must meet the following requirements:

- **Globally Unique**: The global uniqueness of the ID must be guaranteed!
- **High Performance**: The generation speed of distributed IDs must be fast, with minimal local resource consumption.
- **High Availability**: The service generating distributed IDs must ensure availability close to 100%.
- **Convenient and Easy to Use**: It should be ready to use, easy to implement, and quickly integrated!

In addition to these, a good distributed ID should also ensure:

- **Security**: The ID should not contain sensitive information.
- **Ordered Increment**: If IDs are to be stored in a database, the order of IDs can improve database write speed. Moreover, many times, we may directly sort by ID.
- **Specific Business Meaning**: If the generated ID has specific business meaning, it can make problem identification and development more transparent (the ID can indicate which business it belongs to).
- **Independent Deployment**: This means that the distributed system has a separate ID generator service specifically for generating distributed IDs. This decouples the ID generation service from business-related services. However, this also increases the overhead of network calls. Overall, if there are many scenarios requiring distributed IDs, an independently deployed ID generator service is still very necessary.

## Common Solutions for Distributed IDs

### Database

#### Auto-Incrementing Database Primary Key

This method is quite straightforward; it generates a unique ID through the auto-incrementing primary key of a relational database.

![Auto-Incrementing Database Primary Key](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/the-primary-key-of-the-database-increases-automatically.png)

Taking MySQL as an example, we can do it as follows.

**1. Create a database table.**

```sql
CREATE TABLE `sequence_id` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `stub` char(10) NOT NULL DEFAULT '',
  PRIMARY KEY (`id`),
  UNIQUE KEY `stub` (`stub`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

The `stub` field is meaningless; it is just a placeholder to facilitate data insertion or modification. Additionally, a unique index is created for the `stub` field to ensure its uniqueness.

**2. Insert data using `replace into`.**

```java
BEGIN;
REPLACE INTO sequence_id (stub) VALUES ('stub');
SELECT LAST_INSERT_ID();
COMMIT;
```

In this data insertion, we use `replace into` instead of `insert into`. The specific steps are as follows:

- Step 1: Attempt to insert data into the table.
- Step 2: If a duplicate data error occurs due to the primary key or unique index field, delete the conflicting row containing the duplicate key value from the table, and then attempt to insert the data again.

The advantages and disadvantages of this method are also quite clear:

- **Advantages**: Simple to implement, IDs are ordered and incrementing,
