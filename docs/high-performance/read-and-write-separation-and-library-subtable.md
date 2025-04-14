---
title: Explanation of Read-Write Separation and Database Sharding
category: High Performance
head:
  -   - meta
      - name: keywords
        content: read-write separation, database sharding, master-slave replication
  -   - meta
      - name: description
        content: The main purpose of read-write separation is to distribute read and write operations on the database across different database nodes. This can slightly enhance write performance and significantly improve read performance. Read-write separation is based on master-slave replication, and MySQL master-slave replication relies on binlog. Database sharding involves distributing data from a database across different databases. Table sharding involves splitting data from a single table, which can be vertical or horizontal. After introducing database sharding, the system needs to resolve transactional issues, distributed IDs, and the inability to perform join operations.
---

## Read-Write Separation

### What is Read-Write Separation?

As the name implies, **read-write separation mainly aims to distribute read and write operations on the database across different database nodes.** This allows for a slight improvement in write performance and a significant enhancement in read performance.

I created a simple diagram to help those who are not very clear about read-write separation understand better.

![Read-Write Separation Diagram](https://oss.javaguide.cn/github/javaguide/high-performance/read-and-write-separation-and-library-subtable/read-and-write-separation.png)

Generally, we choose one master and multiple slaves, meaning one master database is responsible for writing while the other slave databases handle reading. There will be data synchronization between the master and slave databases to ensure data accuracy in the slave databases. This architecture is relatively simple to implement and aligns with the system's characteristic of having more reads than writes.

### How to Implement Read-Write Separation?

Regardless of the specific implementation方案 for read-write separation, it generally involves the following steps:

1. Deploy multiple databases, selecting one as the master database and one or more as slave databases.
1. Ensure real-time data synchronization between the master and slave databases, which we commonly refer to as **master-slave replication**.
1. The system delegates write requests to the master database and read requests to the slave databases.

For project implementation, there are two commonly used methods:

**1. Proxy Approach**

![Proxy Approach for Read-Write Separation](https://oss.javaguide.cn/github/javaguide/high-performance/read-and-write-separation-and-library-subtable/read-and-write-separation-proxy.png)

We can add a proxy layer between the application and the database. All data requests from the application are handled by the proxy layer, which is responsible for separating read and write requests and routing them to the corresponding databases.

Middleware that provides similar functionality includes **MySQL Router** (official, an alternative to MySQL Proxy), **Atlas** (based on MySQL Proxy), **MaxScale**, and **MyCat**.

One point to highlight about MySQL Router: In the MySQL 8.2 version, MySQL Router can automatically distinguish between read and write operations on the database and route these operations to the correct instances. This is a valuable feature that can optimize database performance and scalability without requiring any changes to the application. For more details, you can refer to the official blog: [MySQL 8.2 – transparent read/write splitting](https://blogs.oracle.com/mysql/post/mysql-82-transparent-readwrite-splitting).

**2. Component Approach**

In this method, we can introduce third-party components to assist with read and write requests.

This is also a method I highly recommend. It is widely used in various internet companies, and there are many practical cases related to it. If you choose to adopt this method, I recommend using `sharding-jdbc`, which can be easily implemented by directly adding the jar package, making it very convenient. Additionally, it saves a lot of operational costs.

You can find the [operations regarding read-write separation for sharding-jdbc](https://shardingsphere.apache.org/document/legacy/3.x/document/cn/manual/sharding-jdbc/usage/read-write-splitting/) on the official shardingsphere documentation.

### What is the Principle of Master-Slave Replication?

MySQL binlog (binary log) mainly records all changes in data within the MySQL database (all DDL and DML statements executed by the database). Therefore, we can synchronize data from the master database to the slave database based on the MySQL binlog logs from the master database.

The specific and detailed process is as follows (image from: [“MySQL Master-Slave Replication on the Same Machine”](https://www.toptal.com/mysql/mysql-master-slave-replication-tutorial)):

![MySQL Master-Slave Replication](https://oss.javaguide.cn/java-guide-blog/78816271d3ab52424bfd5ad3086c1a0f.png)

1. The master database writes changes in the database to the binlog
1. The slave database connects to the master database
1. The slave database creates an I/O thread to request the updated binlog from the master database
1. The master database creates a binlog dump thread to send the binlog, and the I/O thread in the slave is responsible for receiving it
1. The I/O thread in the slave writes the received binlog to the relay log
1. The SQL thread in the slave reads the relay log and synchronizes the data locally (essentially executing the SQL again).

So, do you understand the process of master-slave replication after my explanation?

Whenever you see binlog, you should think of master-slave replication. Of course, aside from master-slave replication, binlog can also help us achieve data recovery.

🌈 Here's an extension:

I wonder if any of you have used an open-source tool from Alibaba called canal. This tool can help us synchronize data between MySQL and other data sources, such as Elasticsearch or another MySQL database. Obviously, the underlying principle of this tool also relies on binlog. The principle of canal is to simulate the master-slave replication process of MySQL, parsing the binlog to synchronize data to other data sources.

Additionally, commonly used distributed caching components like Redis also achieve read-write separation through master-slave replication.

🌕 In summary:

**MySQL master-slave replication relies on binlog. Furthermore, some common tools for synchronizing MySQL data to other data sources (like canal) typically also depend on binlog.**

### How to Avoid Master-Slave Lag?

Read-write separation is very effective for improving database concurrency. However, it also brings a problem: there is lag between the master and slave databases, meaning after writing to the master database, it takes time to synchronize data to the slave database. This time difference leads to inconsistency between the data in the master and slave databases, which we often refer to as **master-slave synchronization lag**.

If our business scenario cannot tolerate master-slave synchronization lag, how can we avoid it? (Note: I'm talking about avoiding rather than just reducing lag.)

Here are two solutions I know of (limited capabilities, welcome to add more), which you can refer to based on your business scenarios.

#### Force Read Requests to Route to the Master Database

Since the data in the slave database is stale, I will just read directly from the master database! This solution may increase the load on the master database, but it is relatively simple to implement and is one of the most commonly used methods I know of.

For example, `Sharding-JDBC` employs this solution. By using the `HintManager` shard key manager of Sharding-JDBC, we can force routes to the master database.

```java
HintManager hintManager = HintManager.getInstance();
hintManager.setMasterRouteOnly();
// Continue JDBC operations
```

For this solution, you can pass all read requests that must obtain the latest data to the master database.

#### Delayed Reading

Some friends might think, if there is lag in master-slave synchronization, why not read after a delay? For instance, if the master-slave synchronization lag is 0.5s, then I will read the data after 1s. This seems convenient! It is convenient, but also quite nonsensical.

However, if you design your business process this way, it will be much better: for scenarios where data is very sensitive, you can avoid performing request operations immediately after completing write requests. For example, after a successful payment, you could redirect to a payment success page, and only return to your accounts when you click back.

#### Summary

Regarding how to avoid master-slave lag, we discussed two solutions here. In fact, the delayed reading solution cannot completely avoid master-slave lag; it can only reduce the probability of lag occurring, and in actual projects, it is generally not used.

Overall, to avoid lag issues, you usually need to force those read requests that must obtain the latest data to be handled by the master database. If the majority of your project's business scenarios do not require high data accuracy, this solution is still a valid option.

### In What Situations Does Master-Slave Lag Occur? How to Minimize Lag?

We have mentioned master-slave lag and methods to avoid it previously; here we will analyze the reasons for master-slave lag and how to minimize it.

To understand in what situations master-slave lag occurs, we first need to understand what master-slave lag is.

MySQL master-slave synchronization lag refers to the situation where the data in the slave database is behind that in the master database. This situation might be caused by the following two reasons:

1. The speed of the slave I/O thread receiving the binlog is slower than that of the master database writing the binlog, leading to the relay log data in the slave being lagged behind the binlog data in the master.
1. The speed at which the slave SQL thread executes the relay log is slower than that at which the slave I/O thread receives the binlog, causing the slave’s data to lag behind the relay log.

Three main time points related to master-slave synchronization are:

1. The master database finishes a transaction, writes to the binlog, and we mark this moment as T1.
1. The slave's I/O thread receives the binlog and writes it to the relay log, we mark this moment as T2.
1. The slave's SQL thread reads from the relay log and synchronizes data locally at the moment marked T3.

Combining what we discussed about master-slave replication principles, we can conclude:

- The difference between T2 and T1 reflects the performance of the slave's I/O thread and the efficiency of network transmission—the smaller this difference, the better the performance and network efficiency of the slave's I/O thread.
- The difference between T3 and T2 reflects the speed of the slave's SQL thread executing—the smaller this difference, the faster the execution speed of the slave's SQL thread.

So in what situations can there be slave lag? Here are a few common situations:

1. **Slave Machine Has Inferior Performance Compared to Master**: The slave's speed in receiving the binlog and writing it to the relay log, as well as executing SQL statements, will be slower (meaning the values of T2-T1 and T3-T2 will be large), causing lag. The solution is to select a slave machine with specifications equivalent to or greater than that of the master or optimize the performance of the slave, such as adjusting parameters, increasing cache, using SSDs, etc.
1. **Excessive Read Requests to the Slave**: The slave must execute all write operations from the master while also responding to read requests; if there are too many read requests, they will consume the slave's CPU, memory, and network resources, affecting the slave's replication efficiency (again, T2-T1 and T3-T2 values will be large, similar to the previous situation). Solutions include introducing caching (recommended), using a one-master multi-slave architecture to distribute read requests across different slaves, or employing other systems to provide querying capabilities, such as integrating the binlog with Hadoop, Elasticsearch, etc.
1. **Large Transactions**: Long-running transactions that have not been committed for a long time can be termed large transactions. Because large transactions take longer to execute, and those on the slave will take more time and resources than those on the master, they are likely to cause master-slave lag. The solution is to avoid making batch modifications to data and try to do so in smaller increments. Similar issues may arise with long-running slow SQL queries; optimization should be done for slow SQL in actual projects.
1. **Too Many Slaves**: The master needs to synchronize the binlog to all slaves; if there are too many slaves, it will increase synchronization time and overhead (where T2-T1 will be comparatively larger, caused by heavier synchronization pressure on the master). The solution is to minimize the number of slaves or to organize slaves into different tiers, with upper-level slaves synchronizing data to lower-level slaves, thus reducing the master’s pressure.
1. **Network Delay**: If the transmission speed between the master and slaves is slow, or issues like packet loss and jitter occur, it can affect the efficiency of binlog transmission, leading to slave delay. Solutions include optimizing the network environment, such as increasing bandwidth, reducing latency, and improving stability.
1. **Single-threaded Replication**: Up until MySQL 5.5, only single-threaded replication was supported. To optimize replication performance, MySQL 5.6 introduced **multi-threaded replication**, with further enhancements in MySQL 5.7.
1. **Replication Mode**: MySQL's default replication is asynchronous, which inherently leads to lag issues. Fully synchronous replication has no lag but offers poor performance. Semi-synchronous replication is a compromise that enhances data safety compared to asynchronous replication while reducing master-slave lag (yet some lag remains). Since MySQL 5.5, MySQL has supported **semi-synchronous replication** in the form of plugins, and MySQL 5.7 introduced **enhanced semi-synchronous replication**.
1. ……

The article [“What Pitfalls Are There in Read-Write Separation?”](https://time.geekbang.org/column/article/77636) in the series [“45 Talks on MySQL Practice”](https://time.geekbang.org/column/intro/100020801?code=ieY8HeRSlDsFbuRtggbBQGxdTh-1jMASqEIeqzHAKrI%3D) also discusses solutions to master-slave lag; interested readers can refer to it for further learning.

## Database Sharding

Read-write separation mainly addresses read concurrency in databases without solving storage issues. Just imagine: **What if the data volume of a single table in MySQL is too large?**

In other words, **how do we alleviate storage pressure on MySQL?**

One answer is **database sharding.**

### What is Database Sharding?

**Database sharding** means distributing data from the database across different databases, which can occur through vertical or horizontal sharding.

**Vertical sharding** divides a single database according to business needs, with different businesses utilizing separate databases to distribute the pressure of one database across multiple databases.

For instance, you can separate user tables, order tables, and product tables into individual user databases, order databases, and product databases.

![Vertical Sharding](./images/read-and-write-separation-and-library-subtable/vertical-slicing-database.png)

**Horizontal sharding** distributes the same table across different databases based on specific rules. Each database may reside on different servers, achieving horizontal scalability and resolving storage and performance bottlenecks of a single table.

For example, if the data volume of the order table is excessively large, you can horizontally segment the order table (horizontal partitioning) and place the resulting two order tables in two different databases.

![Horizontal Sharding](./images/read-and-write-separation-and-library-subtable/horizontal-slicing-database.png)

### What is Table Sharding?

**Table sharding** means splitting the data of a single table, which can also be done through vertical or horizontal methods.

**Vertical table sharding** involves dividing the columns of a data table, breaking a table with many columns into multiple tables.

For example, some columns from the user information table can be separated into their own table.

**Horizontal table sharding** divides the rows of a data table, breaking a table with a large number of rows into multiple tables to alleviate the impact of excessive data volume on performance.

For example, user information tables can be partitioned into multiple tables, which can avoid performance impacts caused by excessive data in a single table.

Horizontal partitioning can only address the issue of a single table having a large volume of data. To enhance performance, we generally opt to distribute the resulting multiple tables across different databases; that is, horizontal table sharding often coexists with horizontal database sharding.

![Table Sharding](./images/read-and-write-separation-and-library-subtable/two-forms-of-sub-table.png)

### When Should We Consider Database Sharding?

Consider database sharding in the following scenarios:

- If the data in a single table exceeds tens of millions, and the database read/write speed is sluggish.
- If the data in the database takes up an increasingly large amount of space, resulting in longer backup times.
- If the application's concurrency is excessively high (alternative performance optimization methods should be prioritized over sharding).

However, the cost of database sharding is quite high, so it should be avoided unless necessary. Moreover, it’s not always the case that a table with tens of millions of records must be sharded; since every table contains different fields, the volume of data each can hold under acceptable performance varies, necessitating analysis of the specific situation.

I previously read an article analyzing “[How much data can a B+ tree with a height of 3 in InnoDB store](https://juejin.cn/post/7165689453124517896)”, which is quite well written, and I recommend taking a look if interested.

### What are Common Sharding Algorithms?

Sharding algorithms mainly address the question of where data should be stored after being horizontally partitioned.

Common sharding algorithms include:

- **Hash-based Sharding**: Calculate the hash of a specified sharding key and determine which table the data should be placed in according to the hash value. Hash sharding is more suitable for scenarios requiring random read/write and less suitable for frequent range queries. Hash sharding can help ensure even distribution of data across tables but is unfriendly to dynamic scaling (e.g., adding a new table or database).
- **Range-based Sharding**: Allocate data according to specific range intervals (e.g., a time interval, ID interval). For instance, records with `id` values from `1 to 299999` go to the first table, while `300000 to 599999` go to the second table. Range sharding is suitable for scenarios requiring frequent range searches and where data distribution is even, but it’s less suitable for random read/write (data isn’t dispersed leading to hot data issues).
- **Mapping Table Sharding**: Use a single separate table (referred to as a mapping table) to store the correspondence between the sharding key and the sharding location. Mapping table sharding can support any type of sharding algorithm, such as hash sharding, range sharding, etc. This strategy allows flexible adjustment of sharding rules without requiring changes to application code or redistributing data. However, this method necessitates maintaining an additional table and increases query overhead and complexity.
- **Consistent Hash Sharding**: Organizes the hash space into a circular structure, mapping both sharding keys and nodes (databases or tables) onto this circle, then determining to which node data or requests should be assigned based on a clockwise rule, thus solving the traditional hash’s unfriendly nature towards dynamic scaling.
- **Geographic Location Sharding**: Many NewSQL databases support geographic location sharding algorithms, which distribute data based on geographical locations (e.g., city, region).
- **Hybrid Algorithm Sharding**: Flexibly combine multiple sharding algorithms, such as combining hash and range sharding.
- ……

### How to Choose a Sharding Key?

The sharding key is the crucial field for data partitioning. The selection of an appropriate sharding key is highly important, as it directly affects data distribution and query efficiency. Generally speaking, a sharding key should have the following characteristics:

- **Universality**: It should cover the vast majority of query scenarios, minimizing the number of shards involved in a single query, thus reducing database pressure.
- **Discreteness**: It should evenly distribute data across all shards to avoid data skew and hot spot issues.
- **Stability**: The value of the sharding key should remain unchanged to prevent data migration and consistency issues.
- **Scalability**: It should allow for dynamic addition and removal of shards to avoid the overhead of data re-sharding.

In actual projects, it's challenging for a sharding key to fulfill all the aforementioned characteristics, requiring some trade-offs. Additionally, the sharding key can be a combination of multiple fields in a table, such as taking the last four digits of a user ID as a suffix for an order ID.

### What Issues Can Database Sharding Bring?

Remember, any technical decision you make at your company should consider not just whether this technology can fulfill our requirements and is suited to the current business scenario, but also consider the costs it incurs.

What challenges can arise in the system after introducing database sharding?

- **Join Operations**: When tables from the same database are distributed across different databases, it becomes impossible to use join operations. This leads to a need for manual data encapsulation; for instance, you might query a piece of data in one database and then look for corresponding data in another database using that data. However, many experienced DBAs in large companies suggest avoiding join operations whenever possible since join operations are inefficient and can negatively impact database sharding. For places where join operations are needed, you could resort to multiple queries at the business layer to assemble the data. However, this method must consider the transactional tolerance of multiple queries from a business standpoint.
- **Transactional Issues**: When tables from the same database are distributed across different databases, standard transactions won't suffice if an individual operation involves multiple databases. In such cases, we need to introduce distributed transactions. There are several common solutions for distributed transaction issues summarized on various websites: <https://javaguide.cn/distributed-system/distributed-transaction.html>.
- **Distributed IDs**: Once sharding is implemented, data is spread across different servers, making it impossible for the database's auto-increment primary key to ensure unique primary key generation. How do we generate global unique primary keys for different data nodes? At this point, we need to introduce distributed IDs into our system. For a detailed introduction and implementation summary of distributed IDs, check out this article I wrote: [Introduction to Distributed IDs & Implementation Summary](https://javaguide.cn/distributed-system/distributed-id.html).
- **Cross-Database Aggregation Query Issues**: Database sharding can complicate conventional aggregation query operations, such as group by and order by. This is because these operations require data aggregation and sorting across multiple shards rather than within a single database. Implementing these operations necessitates writing complex business code or using middleware to coordinate communication and data transmission between shards, leading to increased development and maintenance costs, as well as affecting query performance and scalability.
- ……

Moreover, introducing database sharding typically requires DBA involvement and a greater number of database servers, all of which contribute to cost.

### Are There Any Recommended Solutions for Database Sharding?

Apache ShardingSphere is a distributed database ecosystem that can transform any database into a distributed database and enhance the original database's functionality through data partitioning, elastic scaling, encryption, and other capabilities.

The ShardingSphere project (including Sharding-JDBC, Sharding-Proxy, and Sharding-Sidecar) was donated to Apache by Dangdang and is currently maintained by several prominent figures from JD Digits.

ShardingSphere is undoubtedly the top choice for database sharding! ShardingSphere's functionality is comprehensive; it supports read-write separation and database sharding while also providing distributed transactions, database governance, shadow libraries, data encryption, and desensitization functionalities.

The capabilities offered by ShardingSphere include:

![ShardingSphere Features](https://oss.javaguide.cn/github/javaguide/high-performance/shardingsphere-features.png)

ShardingSphere's advantages include (extracted from the ShardingSphere official documentation: <https://shardingsphere.apache.org/document/current/cn/overview/>):

- **Ultimate performance**: The driver has undergone extensive refinement over years, achieving efficiency close to native JDBC performance.
- **Ecosystem compatibility**: The proxy supports access from any application through MySQL/PostgreSQL protocol, while the driver can interface with any database implementing JDBC standards.
- **Zero-invasion for business**: For scenarios requiring database replacement, ShardingSphere allows for smooth business migration without requiring any alterations to existing applications.
- **Low operational costs**: It maintains low learning and management costs for DBAs while retaining the original technology stack, ensuring user-friendly interaction.
- **Safe and stable**: It provides incremental capabilities based on mature database foundations while balancing security and stability.
- **Elastic scalability**: It has the capacity for smooth online expansion of computing and storage, meeting diverse business demands.
- **Open ecosystem**: It offers customizable solutions for users’ unique needs through multi-layered (core, functionalities, ecosystem) plugin capabilities.

Additionally, ShardingSphere has a complete ecosystem, an active community, comprehensive documentation, with frequent updates and releases.

However, it’s worth reiterating: **Many companies are currently using distributed relational databases like TiDB that eliminate the need for manual sharding (the database layer takes care of this), and they do not have to deal with the various issues that arise from manual sharding. This provides a seamless solution with many built-in practical features (such as seamless expansion and reduction, and cold/hot storage separation)! If conditions allow within the company, I personally recommend this approach!**

### How to Migrate Data After Database Sharding?

After implementing database sharding, how can we migrate data from the old database (single database, single table) to the new database (the database system after sharding)?

A simple and commonly used method is **downtime migration**, where you write a script to transfer data from the old database to the new one. For instance, at 2 AM when system usage is minimal, you can put up a notice stating that the system will undergo maintenance and upgrade for about an hour. Then, you can write a script that synchronizes all data from the old database to the new database.

If you prefer not to have downtime during the migration process, you could consider a **dual-write approach**. The dual-write approach is designed for scenarios where downtime migration is not possible and is somewhat more complex. The specific principle is as follows:

- For updates (insertions, deletions, modifications) in the old database, you also write to the new database (dual writing). If the data being operated on does not exist in the new database, it needs to be inserted. This ensures that the new database contains the latest data.
- During the migration, dual writing will only synchronize data that has been updated from the old database to the new database, you’ll also need to write a script to compare the data between the old and new databases. If any data is missing in the new database, insert it. If the new database has data that isn’t in the old database, delete the corresponding redundant entries in the new database.
- Repeat the above operation until the data in the old and new databases are consistent.

Implementing dual writing can be quite tricky in a project, and problems can easily arise. We could use the aforementioned database synchronization tool Canal for incremental data migration (which still relies on binlog, minimizing development and maintenance costs).

## Summary

- Read-write separation mainly aims to distribute read and write operations on the database across different database nodes. This can slightly improve write performance and significantly enhance read performance.
- Read-write separation is based on master-slave replication, with MySQL master-slave replication relying on binlog.
- **Database sharding** involves distributing data across multiple databases. **Table sharding** refers to splitting data from a single table, which can be vertical or horizontal.
- After introducing database sharding, the system needs to address transactional issues, distributed IDs, and the inability to perform join operations.
- Many companies currently utilize distributed relational databases like TiDB, where manual sharding is unnecessary (the database layer handles it), and companies are spared from resolving the various issues introduced by manual sharding, enjoying many built-in practical features (like seamless scaling and cold/hot storage separation)! If company conditions allow, I personally recommend this option!
- If manual sharding is necessary, ShardingSphere is the first choice! ShardingSphere's functionality is comprehensive; in addition to supporting read-write separation and database sharding, it also provides distributed transactions, database governance, among other features. Additionally, ShardingSphere has a robust ecosystem, an active community, comprehensive documentation, with frequent updates and releases.

<!-- @include: @article-footer.snippet.md -->
