---
title: Summary of Common MongoDB Interview Questions (Part 2)
category: Database
tag:
  - NoSQL
  - MongoDB
---

## MongoDB Indexes

### What are the uses of MongoDB indexes?

Similar to relational databases, MongoDB also has indexes. The main purpose of indexes is to improve query efficiency. Without indexes, MongoDB must perform **collection scans**, which means scanning every document in the collection to select documents that match the query statement. If a suitable index exists for the query, MongoDB can use that index to limit the number of documents it must check. Additionally, MongoDB can return sorted results based on the index order.

While indexes can significantly reduce query time, there is a cost to using and maintaining indexes. When performing write operations, in addition to updating documents, the indexes must also be updated, which inevitably affects write performance. Therefore, it is not recommended to create indexes when there are many write operations and few read operations, or when read operation performance is not a concern.

### What types of indexes does MongoDB support?

**MongoDB supports various types of indexes, including single-field indexes, compound indexes, multi-key indexes, hash indexes, text indexes, and geospatial indexes, each with different use cases.**

- **Single-field index:** An index built on a single field. The sort order of the index creation does not matter; MongoDB can traverse from the beginning or the end.
- **Compound index:** An index built on multiple fields, which can also be called a composite index or a combined index.
- **Multi-key index:** When a field in MongoDB is an array, creating an index on that field is known as a multi-key index. MongoDB will create indexes for each value in the array. This means you can query based on the values inside the array while still using the index.
- **Hash index:** An index based on the hash values of the data, used in hash-sharded clusters.
- **Text index:** Supports full-text search queries on string content. A text index can include any field whose values are strings or arrays of strings. A collection can only have one text search index, but that index can cover multiple fields. Although MongoDB supports full-text indexing, its performance is poor, and it is not recommended for now.
- **Geospatial index:** An index based on latitude and longitude, suitable for 2D and 3D location queries.
- **Unique index:** Ensures that the indexed field does not store duplicate values. If a document violating the unique constraint already exists in the collection, creating a unique index in the background will fail.
- **TTL index:** TTL indexes provide an expiration mechanism, allowing an expiration time to be set for each document. When a document reaches its preset expiration time, it will be deleted.
- ……

### Does the order of fields in a compound index matter?

The order of fields in a compound index is very important. For example, if a compound index consists of `{userid:1, score:-1}`, the compound index first sorts by `userid` in ascending order; then within each value of `userid`, it sorts by `score` in descending order.

![Compound Index](https://oss.javaguide.cn/github/javaguide/database/mongodb/mongodb-composite-index.png)

The sorting method in a compound index determines whether that index can be applied in a query.

Sorting that uses the compound index:

```sql
db.s2.find().sort({"userid": 1, "score": -1})
db.s2.find().sort({"userid": -1, "score": 1})
```

Sorting that does not use the compound index:

```sql
db.s2.find().sort({"userid": 1, "score": 1})
db.s2.find().sort({"userid": -1, "score": -1})
db.s2.find().sort({"score": 1, "userid": -1})
db.s2.find().sort({"score": 1, "userid": 1})
db.s2.find().sort({"score": -1, "userid": -1})
db.s2.find().sort({"score": -1, "userid": 1})
```

We can analyze this through explain:

```sql
db.s2.find().sort({"score": -1, "userid": 1}).explain()
```

### Does a compound index follow the left-prefix principle?

**MongoDB's compound index follows the left-prefix principle**: An index with multiple keys can simultaneously obtain indexes composed of all prefixes of those keys, but does not include subsets other than the left prefix. For example, an index like `{a: 1, b: 1, c: 1, ..., z: 1}` effectively implies indexes like `{a: 1}`, `{a: 1, b: 1}`, `{a: 1, b: 1, c: 1}`, etc., but there will be no index like `{b: 1}` that is not a left prefix index.

### What is a TTL index?

A TTL index provides an expiration mechanism, allowing an expiration time `expireAfterSeconds` to be set for each document. When a document reaches its preset expiration time, it will be deleted. Aside from having the `expireAfterSeconds` attribute, a TTL index is just like a normal index.

Data expiration is useful for certain types of information, such as machine-generated event data, logs, and session information, which only need to be retained in the database for a limited time.

**How TTL Index Works:**

- MongoDB will start a background thread to read the TTL index values to determine if documents have expired, but it will not guarantee that expired data is deleted immediately, as the background thread triggers a deletion task every 60 seconds. If a large amount of data needs to be deleted, it is possible for the previous deletion to not complete before the next task starts, resulting in expired data being retained beyond the 60-second limit.
- For replica sets, the TTL index background process runs only on the Primary node, while it remains idle on Secondary nodes. The deletion of data on Secondary nodes is synchronized from the Primary through the oplog after it has been deleted.

**TTL Index Limitations:**

- TTL indexes are single-field indexes. Compound indexes do not support TTL.
- The `_id` field does not support TTL indexes.
- It is not possible to create a TTL index on capped collections, as MongoDB cannot delete documents from capped collections.
- If a non-TTL index already exists on a field, a TTL index cannot be created on that field.

### What is a covered index query?

According to the official documentation, a covered query is defined as follows:

- All query fields are part of the index.
- All fields returned in the result are contained within the same index.
- The query does not contain any fields equal to `null`.

Since all fields that appear in the query are part of the index, MongoDB does not need to retrieve matching query conditions from the entire data document. Accessing data from the index, which resides in memory, is much faster than reading data via document scanning.

For example, we have the following `users` collection:

```json
{
   "_id": ObjectId("53402597d852426020000002"),
   "contact": "987654321",
   "dob": "01-01-1991",
   "gender": "M",
   "name": "Tom Benzamin",
   "user_name": "tombenzamin"
}
```

We create a compound index in the `users` collection on the fields `gender` and `user_name`:

```sql
db.users.ensureIndex({gender:1,user_name:1})
```

Now, this index will cover the following query:

```sql
db.users.find({gender:"M"},{user_name:1,_id:0})
```

To ensure that the specified index covers the query, you must explicitly specify `_id: 0` to exclude the `_id` field from the result, as the index does not include the `_id` field.

## MongoDB High Availability

### Replica Set

#### What is a replica set?

MongoDB's replica set is a group of mongod processes that maintain the same dataset.

Clients connect to the entire MongoDB replica set, where the primary node is responsible for all writes to the replica set, while secondary nodes can perform read operations. However, by default, the primary node is responsible for all reads within the replica set. If the primary node fails, a new primary node is automatically elected from the secondary nodes to ensure the cluster's ongoing availability, which is transparent to the client.

Typically, a replica set includes one primary node (Primary), multiple secondary nodes (Secondary), and zero or one arbiter node (Arbiter).

- **Primary Node:** The entry point for write operations in the cluster, receiving all write operations and logging all changes to the operation log, known as oplog. A new primary node is automatically elected if the current one fails.
- **Secondary Node:** Synchronizes data from the primary node and elects a new node if the primary node fails. However, a secondary node can be configured with a priority of 0 to prevent it from being elected as the primary.
- **Arbiter Node:** Used to save resources or for disaster recovery across multiple data centers; it only votes during primary node elections and does not store data, ensuring that a node can receive a majority of votes.

The diagram below shows a typical three-member replica set:

![](https://oss.javaguide.cn/github/javaguide/database/mongodb/replica-set-read-write-operations-primary.png)

Data synchronization between the primary and secondary nodes occurs through the **oplog (operation log)**. The oplog is a special **capped collection** in the local database that stores incremental logs produced by write operations, similar to the Binlog in MySQL.

> A capped collection is similar to a fixed-length circular queue where data is appended to the end of the collection. When the collection reaches its maximum capacity, it overwrites the oldest documents. Data in a capped collection is sequentially written to disk, providing very fast I/O speeds, and performance is even better when no indexes are established.

![](https://oss.javaguide.cn/github/javaguide/database/mongodb/replica-set-primary-with-two-secondaries.png)

After a write operation is completed on the primary node, a corresponding log entry is written to the oplog collection, and the secondary nodes continuously pull new logs from this oplog, replaying them locally to achieve data synchronization.

There can be at most one primary node in a replica set. If the current primary node becomes unavailable, an election will determine a new primary node. The rules for node election in MongoDB guarantee that the newly chosen node after a primary failure is the one with the most complete data in the cluster.

#### Why use a replica set?

- **To implement failover:** It provides automatic fault recovery functionality. When the primary node fails, a new primary is automatically elected from the secondary nodes to ensure the normal operation of the cluster, which is transparent to the client.
- **To achieve read-write separation:** We can configure secondary nodes to read data while the primary node is responsible for write operations, thus achieving read-write separation and alleviating the excessive load on the primary node. Prior to MongoDB 4.0, it was not recommended to separate reads and writes if the primary node was not under heavy load, as writes would block reads unless the business did not emphasize response time and was acceptable with a certain delay in reading historical data.

### Sharded Clusters

#### What is a sharded cluster?

A sharded cluster is the distributed version of MongoDB. Unlike replica sets, the data in a sharded cluster is balanced across different shards, significantly increasing the overall data capacity of the cluster and distributing the read and write load across different shards to address the performance bottlenecks of replica sets.

A MongoDB sharded cluster consists of the following three components (the diagram below is sourced from the [official documentation on sharded clusters](https://www.mongodb.com/docs/manual/sharding/)):

![](https://oss.javaguide.cn/github/javaguide/database/mongodb/sharded-cluster-production-architecture.png)

- **Config Servers:** Config servers are essentially a MongoDB replica set that is responsible for storing various metadata and configurations for the cluster, such as shard addresses, Chunks, etc.
- **Mongos:** Router service that does not store actual data. It retrieves the cluster configuration from Config and forwards requests to specific shards, aggregating results and returning them to the client.
- **Shard:** Each shard is a subset of the overall data. Starting from MongoDB 3.6, each shard must be deployed as a replica set architecture.

#### Why use a sharded cluster?

As the amount of data and throughput in a system grows, common solutions include vertical scaling and horizontal scaling.

Vertical scaling achieves this by increasing the capability of a single server, such as disk space, memory capacity, CPU count, etc. Horizontal scaling achieves this by distributing data across multiple servers, adding additional servers as needed to increase capacity.

Similar to Redis Cluster, MongoDB can also implement **horizontal scaling** through sharding. This scaling method is more flexible, can meet the storage needs of larger datasets, and supports higher throughput. Furthermore, the overall cost required for horizontal scaling is lower since it only needs relatively lower-configured single servers, albeit at the cost of increased complexity in infrastructure deployment and maintenance.

This means that when you encounter the following issues, a sharded cluster can be used for resolution:

- Storage capacity is limited by a single machine, meaning disk resources encounter bottlenecks.
- Read and write capabilities are constrained by a single machine, possibly due to CPU, memory, or network card resources encountering bottlenecks, preventing the ability to scale reads and writes.

#### What is a shard key?

**Shard Key** is the premise for data partitioning, enabling data distribution across different servers to lighten the server's load. In other words, the shard key determines how documents within a collection are distributed across multiple shards in the cluster.

A shard key is essentially a field within the document, but it is not an ordinary field and has certain requirements:

- It must appear in all documents.
- It must be an index for the collection and can be a single index or the prefix index of a compound index; it cannot be a multi-index, text index, or geospatial index.
- Before MongoDB 4.2, the values of a document's shard key field were immutable. Since version 4.2, unless the shard key field is the immutable `_id` field, you can update the value of the shard key in the document. Starting from version 5.0, live resharding has been implemented, allowing for complete reshaping of the shard key.
- Its size cannot exceed 512 bytes.

#### How to choose a shard key?

Choosing an appropriate shard key significantly affects the efficiency of sharding and is primarily based on the following four factors (excerpt from [Sharded Cluster Usage Notes - Tencent Cloud Documentation](https://cloud.tencent.com/document/product/240/44611)):

- **Cardinality of values:** It is recommended to have as high cardinality as possible. If a shard key with low cardinality is used, then the total number of chunks will be limited, as there are limited alternative values. As data increases, the size of chunks will become larger, making it very difficult to move chunks during horizontal expansions. For example, choosing age as a cardinality will have a maximum range of only 100, and as data increases, the same value being distributed too much will cause chunk size to exceed chunk size limit, leading to jumbo chunks that cannot migrate, resulting in uneven data distribution and performance bottlenecks.
- **Distribution of values:** It is advisable to have a uniform distribution of values. An unevenly distributed shard key can cause some chunks with a significantly larger amount of data, similarly leading to the above issues of uneven data distribution and performance bottlenecks.
- **Query with shard key:** It is recommended to include the shard key in your queries. Using the shard key for conditional queries allows mongos to directly locate a specific shard; otherwise, mongos needs to distribute the queries to all shards and wait for responses to return.
- **Avoid monotonic increase or decrease:** Monotonic increasing shard keys may lead to small data file movements but concentrated writes, resulting in a continually growing data quantity in the last chunk and causing frequent migrations. The same applies for monotonic decreases.

In summary, when choosing a shard key, consider the above four conditions and try to meet as many of those conditions as possible to reduce the impact of MoveChunks on performance and achieve the optimal performance experience.

#### What are the sharding strategies?

MongoDB supports two sharding algorithms to meet different query needs (excerpt from [Introduction to MongoDB Sharded Clusters - Alibaba Cloud Documentation](https://help.aliyun.com/document_detail/64561.html?spm=a2c4g.11186623.0.0.3121565eQhUGGB#h2--shard-key-3)):

**1. Range-based sharding:**

![](https://oss.javaguide.cn/github/javaguide/database/mongodb/example-of-scope-based-sharding.png)

MongoDB splits data into different chunks based on the ranges of the shard key (Shard Key) values, with each chunk containing data within a certain range. Range sharding is more efficient when the cardinality of the shard key is high, frequency is low, and values are not monotonically changing.

- Advantages: Mongos can quickly locate the required data for requests and forward the requests to the corresponding shard nodes.
- Disadvantages: May lead to data being unevenly distributed across shard nodes, easily causing read/write hotspots and lacking write dispersion.
- Applicable scenarios: When the values of the shard key are neither monotonically increasing nor decreasing, the cardinality of the shard key values is high with a low repetition frequency, and range queries are needed.

**2. Hash-based sharding:**

![](https://oss.javaguide.cn/github/javaguide/database/mongodb/example-of-hash-based-sharding.png)

MongoDB computes the hash value of a single field as an index value and splits data into different chunks based on the range of hash values.

- Advantages: Can distribute data more evenly across shard nodes, exhibiting write dispersion.
- Disadvantages: Not suitable for range queries; range queries require distributing read requests to all shard nodes.
- Applicable scenarios: When the values of the shard key exhibit monotonic increases or decreases, the cardinality of shard key values is high with a low repetition frequency, and data needs to be randomly distributed for writing or reading request randomness is high.

In addition to the above two sharding strategies, you can also configure **composite shard keys**, for example, one low cardinality key combined with a monotonically increasing key.

#### How is sharded data stored?

**Chunk** is a core concept in MongoDB sharded clusters, essentially a logical unit of data composed of a set of documents. Each chunk contains data of certain ranges of shard keys, with non-overlapping ranges and a union that represents all data, aligning with the concept of **partitioning** in discrete mathematics.

Sharded clusters do not record where each data point is located within which shard; instead, they keep track of which chunks are on which shards and which data each chunk encompasses.

By default, the maximum value of a chunk is set to 64MB (adjustable, ranging from 1 to 1024 MB. Unless a specific need arises, it is recommended to keep the default value). When inserting, updating, or deleting data, if mongos detects that the target chunk's size or its data exceeds the limit, it will trigger a **chunk split**.

![Chunk Splitting](https://oss.javaguide.cn/github/javaguide/database/mongodb/chunk-splitting-shard-a.png)

As data increases, chunks will become increasingly more numerous. At this point, the quantity of chunks on each shard may become unbalanced. The **balancer** component in mongos will execute automatic balancing, attempting to maintain an even quantity of chunks across each shard, a process known as **rebalance**. By default, rebalance for databases and collections is enabled.

As shown in the diagram below, as data is inserted leading to chunk splits, shards A and B may have 3 chunks, while shard C has only one; at this point, one chunk from B may be migrated to shard C to achieve balanced cluster data.

![Chunk Migration](https://oss.javaguide.cn/github/javaguide/database/mongodb/mongo-reblance-three-shards.png)

> The balancer is a background process running on the Primary node of the Config Server (since MongoDB version 3.4) that monitors the number of chunks on each shard and migrates chunks when the quantity on a shard exceeds a threshold.

Chunks only split, they do not merge, even if the value of chunkSize increases.

Rebalance operations are resource-intensive; thus, we can reduce their impact on regular MongoDB operations by executing them during off-peak business hours, pre-splitting, or setting rebalance time windows.

#### What is the principle behind chunk migration?

For a detailed introduction to the principles of chunk migration, it is recommended to read the article on [Understanding MongoDB chunk migration in a single article](https://mongoing.com/archives/77479) from the MongoDB Chinese community.

## Recommended Learning Resources

- [MongoDB Chinese Manual | Official Documentation in Chinese](https://docs.mongoing.com/) (Recommended): Based on version 4.2, continuously synchronized with the latest official version.
- [MongoDB Beginner's Tutorial — Learn MongoDB in 7 Days](https://mongoing.com/archives/docs/mongodb%e5%88%9d%e5%ad%a6%e8%80%85%e6%95%99%e7%a8%8b/mongodb%e5%a6%82%e4%bd%95%e5%88%9b%e5%bb%ba%e6%95%b0%e6%8d%ae%e5%ba%93%e5%92%8c%e9%9b%86%e5%90%88): A quick introduction.
- [SpringBoot Integration with MongoDB Practice - 2022](https://www.cnblogs.com/dxflqm/p/16643981.html): A great introductory article on MongoDB, mainly focusing on basic CRUD operations using the MongoDB Java client.

## References

- MongoDB Official Documentation (main reference material, take as authoritative): <https://www.mongodb.com/docs/manual/>
- "MongoDB: The Definitive Guide"
- Indexes - MongoDB Official Documentation: <https://www.mongodb.com/docs/manual/indexes/>
- MongoDB - Index Knowledge - Programmer Xiangzai - 2022: <https://fatedeity.cn/posts/database/mongodb-index-knowledge.html>
- MongoDB - Index: <https://www.cnblogs.com/Neeo/articles/14325130.html>
- Sharding - MongoDB Official Documentation: <https://www.mongodb.com/docs/manual/sharding/>
- Introduction to MongoDB Sharded Clusters - Alibaba Cloud Documentation: <https://help.aliyun.com/document_detail/64561.html>
- Usage Notes for Sharded Clusters - Tencent Cloud Documentation: <https://cloud.tencent.com/document/product/240/44611>

<!-- @include: @article-footer.snippet.md -->
