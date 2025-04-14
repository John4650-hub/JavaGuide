---
title: ZooKeeper Practical Guide
category: Distributed
tag:
  - ZooKeeper
---

This article will briefly demonstrate the use of common ZooKeeper commands and the basic usage of the ZooKeeper Java client, Curator. The topics covered are the most basic operations and can meet the basic needs of daily work.

If there are any areas in the article that need improvement or enhancement, feel free to point them out in the comments section for our mutual progress!

## ZooKeeper Installation

### Installing ZooKeeper Using Docker

**a. Download ZooKeeper using Docker**

```shell
docker pull zookeeper:3.5.8
```

**b. Run ZooKeeper**

```shell
docker run -d --name zookeeper -p 2181:2181 zookeeper:3.5.8
```

### Connecting to the ZooKeeper Service

**a. Enter the ZooKeeper container**

First, use `docker ps` to check the ContainerID of ZooKeeper, and then use the command `docker exec -it ContainerID /bin/bash` to enter the container.

**b. Navigate to the bin directory and connect to the ZooKeeper service using the command `./zkCli.sh -server 127.0.0.1:2181`**

```bash
root@eaf70fc620cb:/apache-zookeeper-3.5.8-bin# cd bin
```

If you see the following message successfully printed in the console, it indicates that you have successfully connected to the ZooKeeper service.

![Connect to ZooKeeper Service](https://oss.javaguide.cn/github/javaguide/distributed-system/zookeeper/connect-zooKeeper-service.png)

## Common ZooKeeper Commands Demonstration

### View Common Commands (help Command)

Use the `help` command to view common ZooKeeper commands.

### Create Node (create Command)

Use the `create` command to create the node1 in the root directory, with the associated string "node1".

```shell
[zk: 127.0.0.1:2181(CONNECTED) 34] create /node1 "node1"
```

Use the `create` command to create the node1.1 under node1, with the associated content as the number 123.

```shell
[zk: 127.0.0.1:2181(CONNECTED) 1] create /node1/node1.1 123
Created /node1/node1.1
```

### Update Node Data Content (set Command)

```shell
[zk: 127.0.0.1:2181(CONNECTED) 11] set /node1 "set node1"
```

### Retrieve Node Data (get Command)

The `get` command can retrieve the data content and status of the specified node, showing that we have already changed the node data to "set node1" using the `set` command.

```shell
[zk: zookeeper(CONNECTED) 12] get -s /node1
set node1
cZxid = 0x47
ctime = Sun Jan 20 10:22:59 CST 2019
mZxid = 0x4b
mtime = Sun Jan 20 10:41:10 CST 2019
pZxid = 0x4a
cversion = 1
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 9
numChildren = 1
```

### View Subnodes of a Directory (ls Command)

Use the `ls` command to view the nodes in the root directory.

```shell
[zk: 127.0.0.1:2181(CONNECTED) 37] ls /
[dubbo, ZooKeeper, node1]
```

Use the `ls` command to view the nodes under the node1 directory.

```shell
[zk: 127.0.0.1:2181(CONNECTED) 5] ls /node1
[node1.1]
```

The `ls` command in ZooKeeper is similar to the `ls` command in Linux; this command will list all subnode information under the absolute path (only listing 1 level, not recursively).

### View Node Status (stat Command)

Use the `stat` command to view the node status.

```shell
[zk: 127.0.0.1:2181(CONNECTED) 10] stat /node1
cZxid = 0x47
ctime = Sun Jan 20 10:22:59 CST 2019
mZxid = 0x47
mtime = Sun Jan 20 10:22:59 CST 2019
pZxid = 0x4a
cversion = 1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 11
numChildren = 1
```

The information displayed above, such as cversion, aclVersion, numChildren, etc., has been introduced in my previous article “[Summary of ZooKeeper Related Concepts (Beginner)](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html)”.

### View Node Information and Status (ls2 Command)

The `ls2` command is more like a combination of the `ls` and `stat` commands. The information returned by the `ls2` command includes 2 parts:

1. List of subnodes
1. The current node's stat information.

```shell
[zk: 127.0.0.1:2181(CONNECTED) 7] ls2 /node1
[node1.1]
cZxid = 0x47
ctime = Sun Jan 20 10:22:59 CST 2019
mZxid = 0x47
mtime = Sun Jan 20 10:22:59 CST 2019
pZxid = 0x4a
cversion = 1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 11
numChildren = 1
```

### Delete Node (delete Command)

This command is straightforward, but it is crucial to note that if you want to delete a node, that node must have no children.

```shell
[zk: 127.0.0.1:2181(CONNECTED) 3] delete /node1/node1.1
```

Later, I will introduce the use of the Java client API and the open-source ZooKeeper clients ZkClient and Curator.

## Simple Usage of ZooKeeper Java Client Curator

Curator is an open-source ZooKeeper Java client framework developed by Netflix. Compared to the built-in ZooKeeper client, Curator's encapsulation is more comprehensive, allowing various APIs to be used more conveniently.

![](https://oss.javaguide.cn/github/javaguide/distributed-system/zookeeper/curator.png)

Now, let's briefly demonstrate the usage of Curator!

Curator version 4.0+ has good support for ZooKeeper 3.5.x. Before starting, please add the following dependencies to your project.

```xml
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-framework</artifactId>
    <version>4.2.0</version>
</dependency>
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>4.2.0</version>
</dependency>
```

### Connecting to ZooKeeper Client

Create a `CuratorFramework` object through `CuratorFrameworkFactory`, and then call the `start()` method on the `CuratorFramework` object!

```java
private static final int BASE_SLEEP_TIME = 1000;
private static final int MAX_RETRIES = 3;

// Retry strategy. Retry 3 times, and will increase the sleep time between retries.
RetryPolicy retryPolicy = new ExponentialBackoffRetry(BASE_SLEEP_TIME, MAX_RETRIES);
CuratorFramework zkClient = CuratorFrameworkFactory.builder()
    // the server to connect to (can be a server list)
    .connectString("127.0.0.1:2181")
    .retryPolicy(retryPolicy)
    .build();
zkClient.start();
```

Here are explanations of some basic parameters:

- `baseSleepTimeMs`: The initial time to wait between retries
- `maxRetries`: The maximum number of retries
- `connectString`: The list of servers to connect to
- `retryPolicy`: The retry policy

### CRUD Operations on Data Nodes

#### Create Node

As we discussed in [Common Concepts of ZooKeeper](./zookeeper-intro.md), we generally categorize znodes into 4 major types:

- **Persistent (PERSISTENT) Nodes**: Once created, they always exist even if the ZooKeeper cluster crashes, until deleted.
- **Ephemeral (EPHEMERAL) Nodes**: The lifecycle of ephemeral nodes is bound to the **client session**; **the session disappears, and the node disappears**. Moreover, ephemeral nodes **can only be leaf nodes**, and cannot have child nodes.
- **Persistent Sequential (PERSISTENT_SEQUENTIAL) Nodes**: In addition to having the characteristics of persistent nodes, the names of child nodes also have order. For example, `/node1/app0000000001`, `/node1/app0000000002`.
- **Ephemeral Sequential (EPHEMERAL_SEQUENTIAL) Nodes**: In addition to having the characteristics of ephemeral nodes, the names of child nodes also have order.

When using ZooKeeper, you will find that the `CreateMode` class has a total of 7 types of znodes, but the most commonly used are the 4 types mentioned above.

**a. Create Persistent Node**

You can create persistent nodes in the following two ways.

```java
// Note: The following code will cause an error; the specific reason will be explained later
zkClient.create().forPath("/node1/00001");
zkClient.create().withMode(CreateMode.PERSISTENT).forPath("/node1/00002");
```

However, running the above code will raise an error because the parent node `node1` has not been created yet.

You can first create the parent node `node1`, and then the above code will not cause an error.

```java
zkClient.create().forPath("/node1");
```

A more recommended way is to use the following line of code. **`creatingParentsIfNeeded()` ensures that if the parent node does not exist, it will be created automatically, which is very useful.**

```java
zkClient.create().creatingParentsIfNeeded().withMode(CreateMode.PERSISTENT).forPath("/node1/00001");
```

**b. Create Ephemeral Node**

```java
zkClient.create().creatingParentsIfNeeded().withMode(CreateMode.EPHEMERAL).forPath("/node1/00001");
```

**c. Create Node and Specify Data Content**

```java
zkClient.create().creatingParentsIfNeeded().withMode(CreateMode.EPHEMERAL).forPath("/node1/00001","java".getBytes());
zkClient.getData().forPath("/node1/00001");// Retrieve the data content of the node; the result is a byte array
```

**d. Check if the Node Was Created Successfully**

```java
zkClient.checkExists().forPath("/node1/00001");// If not null, it indicates that the node was created successfully
```

#### Delete Node

**a. Delete a Child Node**

```java
zkClient.delete().forPath("/node1/00001");
```

**b. Delete a Node and All Its Children**

```java
zkClient.delete().deletingChildrenIfNeeded().forPath("/node1");
```

#### Retrieve/Update Node Data Content

```java
zkClient.create().creatingParentsIfNeeded().withMode(CreateMode.EPHEMERAL).forPath("/node1/00001","java".getBytes());
zkClient.getData().forPath("/node1/00001");// Retrieve the data content of the node
zkClient.setData().forPath("/node1/00001","c++".getBytes());// Update the data content of the node
```

#### Retrieve All Child Node Paths of a Specific Node

```java
List<String> childrenPaths = zkClient.getChildren().forPath("/node1");
```

<!-- @include: @article-footer.snippet.md -->
