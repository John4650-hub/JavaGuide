I don't...
title: ZooKeeper
Category: Distribution
Tag:

- ZooKeeper.
  I don't...

This article briefly demonstrates the use of ZooKeeper's common commands and the basic use of the ZooKeeper Java client Curator. The content presented is the most basic of operations and meets the basic requirements of daily work.

If there is anything in the articles that needs to be improved and perfected, we welcome comments in the comment section for common progress!

# ZooKeeper Install

## Install ZooKeeper using Docker

**a. Download ZooKeeper** with Docker

```shell
docker pull zookeeper:3.5.8
```

**b. Run ZooKeeper**

```shell
docker run -d --name zookeeper -p 2181:2181 zookeeper:3.5.8
```

## Connect ZooKeeper Service

**a. Enter the ZooKeeper container**

See the ContainerID of ZooKeeper first with `docker ps` and then enter the container with the `docker exec -it ContainerID /bin/bash` command.

**b. Enter the bin directory and then connect to ZooKeeper services by `./zkCli.sh --server 127.0.0.1:2181` command**

```bash
root@eaf70fc620cb:/apache-zookeeper-3.5.8-bin# cd bin
```

If you see the console print out the following information, you have successfully connected to the ZooKeeper service.

![Connect ZooKeeper Service](https://oss.javaguide.cn/github/javaguide/distributed-system/zookeeper/connect-zooKeeper-service.png)

# ZooKeeper Common Command Demonstration

## View commonly used commands (help commands)

View ZooKeeper Common Commands by `help`

## Create Node (create command)

Creates node1 in the root directory by `create` command. The string associated with it is "node1".

```shell
[zk: 127.0.0.1:2181 (CONNECTED) 34] create /node1 "node1"
```

Creates node1 in the root directory by `create` command, which is associated with the number 123.

```shell
[zk: 127.0.0.1:2181 (CONNECTED) 1] create /node1/node1.1 123
Created /node1/node1.1
```

## Update node data content (set command)

```shell
[zk: 127.0.0.1:2181 (CONNECTED) 11] set /node1 "set node1"
```

## Get Node Data (get command)

The `get` command provides access to the data content and the state of the node specified, as can be seen from the `set` command that we have changed the node data content to "set node1".

```shell
[zk: 127.0.0.1:2181 (CONNECTED) 12] get /node1
set node1
cZxid = 0x47
ctime = Sun Jan 20 22:59:59 CST 2019
mZxid = 0x4b
mtime = Sun Jan 20 22:41:10 CST 2019
pZxid = 0x4a
cversion = 1
DataVersion = 1
aclVersion = 0
EphemeralOwner = 0x0
DataLength = 9
NumChildren = 1
```

## View subnodes (ls command) under a directory

View nodes under the root directory by `ls` command.

```shell
[zk: 127.0.0.1:2181 (CONNECTED) 37] ls /
[dubbo, ZooKeeper, node1]
```

View node1 under `ls` command.

```shell
[zk: 127.0.0.1:2181 (CONNECTED) 5] ls /node1
[node1.1]
```

The `ls` command in ZooKeeper is similar to the `ls` in the Linux command, which lists all subnode information under the absolute path (1 level, not recursive).

## View Node Status (stat command)

View node status by `stat` command.

```shell
[zk: 127.0.0.1:2181 (CONNECTED) 10] stat /node1
cZxid = 0x47
ctime = Sun Jan 20 22:59:59 CST 2019
mZxid = 0x47
mtime = Sun Jan 20 22:59:59 CST 2019
pZxid = 0x4a
cversion = 1
DataVersion = 0
aclVersion = 0
EphemeralOwner = 0x0
DataLength = 11
NumChildren = 1
```

Some of the information shown above, such as cversion, aclVersion
