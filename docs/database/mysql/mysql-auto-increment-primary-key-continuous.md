---
title: Is MySQL Auto-Increment Primary Key Always Continuous?
category: Database
tag:
  - MySQL
  - Big Company Interviews
---

> Author: Feitian Xiaoniurou
>
> Original: <https://mp.weixin.qq.com/s/qci10h9rJx_COZbHV3aygQ>

As we all know, auto-increment primary keys allow clustered indexes to maintain an increasing order of insertion, avoiding random queries and thus improving query efficiency.

However, in reality, MySQL's auto-increment primary keys do not guarantee that they will always be continuously increasing.

Let's look at an example by creating a table as shown below:

![](https://oss.javaguide.cn/p3-juejin/3e6b80ba50cb425386b80924e3da0d23~tplv-k3u1fbpfcp-zoom-1.png)

## Where is the Auto-Increment Value Stored?

Using `insert into test_pk values(null, 1, 1)` to insert a row of data, then executing the `show create table` command to check the table structure definition:

![](https://oss.javaguide.cn/p3-juejin/c17e46230bd34150966f0d86b2ad5e91~tplv-k3u1fbpfcp-zoom-1.png)

The structure definition of the above table is stored in a local file with the `.frm` suffix, which can be found in the data folder under the MySQL installation directory:

![](https://oss.javaguide.cn/p3-juejin/3ec0514dd7be423d80b9e7f2d52f5902~tplv-k3u1fbpfcp-zoom-1.png)

From the above table structure, we can see that the table definition contains `AUTO_INCREMENT=2`, indicating that the next time data is inserted, if an auto-increment value is needed, it will generate id = 2.

However, it is important to note that the auto-increment value is not stored in this table structure, i.e., the `.frm` file. Different engines have different strategies for storing auto-increment values:

1. The MyISAM engine stores the auto-increment value in the data file.

1. The InnoDB engine actually stores the auto-increment value in memory and does not persist it. When the table is first opened, it looks for the maximum auto-increment value `max(id)` and then sets `max(id)+1` as the current auto-increment value for the table.

For example: if the current maximum id in the table is 1, and AUTO_INCREMENT=2, right? If we delete the row with id=1, AUTO_INCREMENT remains 2.

![](https://oss.javaguide.cn/p3-juejin/61b8dc9155624044a86d91c368b20059~tplv-k3u1fbpfcp-zoom-1.png)

But if we immediately restart the MySQL instance, after the restart, the AUTO_INCREMENT for this table will change to 1. This means that restarting MySQL may modify the AUTO_INCREMENT value of a table.

![](https://oss.javaguide.cn/p3-juejin/27fdb15375664249a31f88b64e6e5e66~tplv-k3u1fbpfcp-zoom-1.png)

![](https://oss.javaguide.cn/p3-juejin/dee15f93e65d44d384345a03404f3481~tplv-k3u1fbpfcp-zoom-1.png)

The above is based on my local MySQL 5.x version experiment. In fact, **after MySQL 8.0, the change records of auto-increment values are stored in the redo log, providing the capability for auto-increment value persistence**, meaning that "if a restart occurs, the table's auto-increment value can be restored to its value before the MySQL restart" based on the redo log.

In other words, for the above example, after restarting the instance, the AUTO_INCREMENT for this table is still 2.

After understanding where MySQL auto-increment values are stored, let's look at the mechanism for modifying auto-increment values, which leads us to the first scenario of non-continuous auto-increment values.

## Scenarios of Non-Continuous Auto-Increment Values

### Scenario 1: Non-Continuous Auto-Increment Values

In MySQL, if the id field is defined as AUTO_INCREMENT, the behavior of the auto-increment value when inserting a row of data is as follows:

- If the id field is specified as 0, null, or not specified, the current AUTO_INCREMENT value of the table is filled into the auto-increment field;
- If the id field is specified with a specific value during insertion, the
