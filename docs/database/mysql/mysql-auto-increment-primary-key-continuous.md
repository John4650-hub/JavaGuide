I don't...
MySQL self-enhanced primary key must be continuous?
Category: Database
Tag:

- MySQL
- Big factory interview
  I don't...

> Author: Flying Beef

Original language: <https://mp.weixin.qq.com/s/qci10h9rJx_COZbHV3aygQ>

It is well known that a self-enhancement primary key will allow a conglomerate index to be inserted as incrementally as possible, avoiding random queries and thus increasing the efficiency of queries.

In practice, however, MySQL's self-enhancement key does not guarantee that it must be incremental.

For example, the following table was created:

![Table Example](https://oss.javaguide.cn/p3-juejin/3e6b80ba50cb425386b80924e3da0d23~tplv-k3u1fbpfcp-zoom-1.png)

Where do you keep your value added?

Use `insert into test_pk values (null, 1, 1)` to insert a line of data and execute the `show create table` command to look at the structure definition of the table:

![Table Structure](https://oss.javaguide.cn/p3-juejin/c17e46230bd340966f0d86b2ad5e91~tplv-k3u1fbpffcp-zoom-1.png)

The structure definition of the above table is stored in a local file with the suffix name `.frm`, which can be found in the `.frm` file under the Data folder in the MySQL installation directory:

![Data Folder](https://oss.javaguide.cn/p3-juejin/3ec0514dd7be423d80b9e7f2d52f5902~tplv-k3u1fbpfcp-zoom-1.png)

As can be seen from the above table structure, an `AUTO_INCREMENT=2` appears in the table definition, which indicates that the next insertion of data will generate id = 2 if it is necessary to automatically generate self-added.

It should be noted, however, that self-value-added does not exist in this table structure, i.e., `.frm`, and different engines have different retention strategies for self-value-added:

1. Self-value-added of MyISAM engine stored in data files

1. The value added of the InnoDB engine is actually stored in memory and not sustained. When the table is opened for the first time, the maximum self-value `max(id)` is sought, and `max(id)+1` is then used as the current self-value added in this table.

For example, the largest id in the current data line in our table is 1, AUTO_INCREMENT=2, right? That's when we delete id=1 line, AUTO_INCREMENT remains 2.

![Deleted ID](https://oss.javaguide.cn/p3-juejin/61b8dc95624044a86d91c368b20059~tplv-k3u1fbpffcp-zoom-1.png)

If the MySQL instance is restarted immediately, then the AUTO_INCREMENT becomes 1. In other words, MySQL restart may modify the value of a table's AUTO_INCREMENT.

![Restart Example](https://oss.javaguide.cn/p3-juejin/27fdb15375664249a31f88b64e6e5e6e66~tplv-k3u1fbpfcp-zoom-1.png)

![Another Restart Example](https://oss.javaguide.cn/p3-juejin/dee15f93e65d44d44d384345a03404f3481~tplv-k3u1fbpffcp-zoom-1.png)

The above is an experiment in my local version of MySQL 5.x, and in fact, **after MySQL 8.0, a record of changes in value added has been placed in the redo log, providing the ability to sustain value added**, i.e., "If a restart occurs, the value added in the table can be restored to the value before MySQL restart in redo log."

That is, for this example, AUTO_INCREMENT is still 2 after restarting the example.

We understand where the value added has been since the value added was stored, and we look at the value-added modification mechanism, and we use it to generate the first scenario of value-added discontinuity.

# The scenes of value-added

## The scene of a lack of self-worth 1

In MySQL, if the field id is defined as AUTO_INCREMENT, when inserting a line of data, the self-added behavior is as follows:

- If the id field is specified as 0, null, or no value at the time the data is inserted, then the current AUTO_INCREMENT value of this table is filled in the add-on field;
- If the id field specifies a specific value when the data is inserted, use the value specified in the statement
