---
title: Character Set Explained
category: Database
tag:
  - Database Basics
---

MySQL's character encoding includes two implementations of UTF-8 encoding: **`utf8`** and **`utf8mb4`**.

If you use **`utf8`**, storing emoji symbols and some more complex Chinese characters or traditional characters will lead to errors.

Why does this happen? This article will provide you with an explanation from the source.

## What is a character set?

Characters are the general term for various letters and symbols, including letters from different countries, punctuation marks, emojis, numbers, and so on. A **character set** is a collection of a series of characters. There are many types of character sets, and the range of characters that each set can represent is usually different; for example, some character sets cannot represent Chinese characters.

**Computers can only store binary data; how should characters like English, Chinese, and emojis be stored?**

We need to establish a correspondence between these characters and binary data, for example, the character "a" corresponds to "01100001," and conversely, "01100001" corresponds to "a." The process of mapping characters to binary data is called "**character encoding**," and the process of interpreting binary data back into characters is called "**character decoding**."

## What is character encoding?

Character encoding is a method that converts characters in a character set to binary data in a computer and vice versa, which can be seen as a mapping rule. In other words, the purpose of character encoding is to enable computers to store and transmit various texts.

Each character set has its own character encoding rules. Common character set encoding rules include ASCII encoding, GB2312 encoding, GBK encoding, GB18030 encoding, Big5 encoding, UTF-8 encoding, UTF-16 encoding, and so on.

## What are some common character sets?

Common character sets include: ASCII, GB2312, GB18030, GBK, Unicode, etc.

The main differences among different character sets are:

- The range of characters they can represent
- Encoding methods

### ASCII

**ASCII** (**A**merican **S**tandard **C**ode for **I**nformation **I**nterchange) is a character set primarily for modern American English (this is also the limitation of the ASCII character set).

**Why wasn't the ASCII character set designed to accommodate other characters, such as Chinese?** Because computers were invented by Americans, and at the time, the development of computers was still in a relatively primitive stage and had not been used extensively in other countries. Therefore, when the ASCII character set was published in the U.S., compatibility with other countries' languages was not considered.

The ASCII character set currently defines 128 characters, 33 of which are control characters (such as carriage return and delete) and cannot be displayed.

An ASCII code has a length of one byte, which is 8 bits in total; for example, the ASCII code for "a" is "01100001." However, the highest bit is 0, merely used for parity, while the remaining 7 bits use 0 and 1 combinations, so the ASCII character set can define 128 (2^7) characters.

However, the number of characters represented by ASCII is quite limited. Later on, people expanded it to create the **ASCII extended character set**. The ASCII extended character set uses 8 bits to represent a character, allowing it to define 256 (2^8) characters.

![ASCII Character Encoding](https://oss.javaguide.cn/github/javaguide/csdn/c1c6375d08ca268690cef2b13591a5b4.png)

### GB2312

As mentioned above, the ASCII character set is suitable mainly for modern American English. Consequently, many countries have developed character sets suitable for their own languages.

The GB2312 character set is a character set that is friendly to Chinese characters, encompassing over 6700 Chinese characters, covering most commonly used Chinese characters. However, the GB2312 character set does not support most rare characters and traditional Chinese characters.

For English characters, GB2312 encoding is the same as ASCII code and requires 1 byte of encoding. Non-English characters require 2 bytes of encoding.

### GBK

The GBK character set can be seen as an extension of the GB2312 character set, and it is compatible with the GB2312 character set and includes over 20,000 Chinese characters.

The 'K' in GBK stands for "Kuo Zhan" (扩展), which means "expansion" in Chinese.

### GB18030

GB18030 is fully compatible with the GB2312 and GBK character sets, incorporates the characters of China's ethnic minorities, and includes Japanese and Korean Chinese characters, making it the most comprehensive Chinese character set to date, encompassing over 70,000 Chinese characters.

### BIG5

BIG5 is mainly targeted at traditional Chinese, containing over 13,000 Chinese characters.

### Unicode & UTF-8

To better suit local languages, a variety of character sets have been born.

As we mentioned before, there are differences in the range of characters that different character sets can represent and their encoding rules. This leads to a very serious problem: **using the wrong encoding method to view a file containing characters can lead to garbled text.**

For example, opening a file encoded in GB2312 using UTF-8 will result in garbled text. For instance, the hexadecimal value of the Chinese character "牛" in GB2312 encoding is "C5A3," but when "C5A3" is decoded using UTF-8, the result is "ţ."

You can perform encoding and decoding online using this website: <https://www.haomeili.net/HanZi/ZiFuBianMaZhuanHuan>

![](https://oss.javaguide.cn/javaguide/csdn/836c49b117ee4408871b0020b74c991d.png)

Thus, we understand the essence of garbled text: **different or incompatible character sets are used during encoding and decoding.**

![](https://oss.javaguide.cn/javaguide/a8808cbabeea49caa3af27d314fa3c02-1.jpg)

To resolve this issue, people thought, "If we could have a character set that includes all characters in the world, that would be great!"

Then, **Unicode** was born with this mission.

The Unicode character set contains almost all known characters in the world. However, Unicode does not stipulate how these characters should be stored (that is, how to represent these characters using binary data).

Thus, **UTF-8** (**8**-bit **U**nicode **T**ransformation **F**ormat) came into existence. Similar formats include UTF-16, UTF-32.

UTF-8 uses 1 to 4 bytes to encode each character, UTF-16 uses 2 or 4 bytes for each character, and UTF-32 uses a fixed 4 bytes for each character.

UTF-8 can automatically choose the encoding length based on different symbols: English characters only require 1 byte, just like the ASCII character set. Therefore, for English characters, UTF-8 encoding is the same as ASCII code.

UTF-32 has the simplest rules, but also a significant downside; for characters like English letters, it consumes four times the space of UTF-8.

**UTF-8** is currently the most widely used character encoding.

![](https://oss.javaguide.cn/javaguide/1280px-Utf8webgrowth.svg.png)

## MySQL Character Set

MySQL supports many character sets, such as GB2312, GBK, BIG5, and various Unicode character sets (UTF-8 encoding, UTF-16 encoding, UCS-2 encoding, UTF-32 encoding, etc.).

### Viewing Supported Character Sets

You can view supported character sets using the `SHOW CHARSET` command, which supports like and where clauses.

![](https://oss.javaguide.cn/javaguide/image-20211008164229671.png)

### Default Character Set

In MySQL 5.7, the default character set is `latin1`; in MySQL 8.0, the default character set is `utf8mb4`.

### Hierarchical Levels of Character Sets

MySQL character sets have the following hierarchical levels:

- `server` (MySQL instance level)
- `database` (database level)
- `table` (table level)
- `column` (field level)

Their priority can be simply considered increasing from top to bottom, meaning that the `column` priority is higher than that of `table` and other levels. For example, if the MySQL instance level character set is `utf8mb4` and a specific table's character set is `latin1`, then all fields in that table will default to `latin1` unless otherwise specified.

#### server

The default values for the `server` level character set vary by MySQL version. In MySQL 5.7, the default value is `latin1`; in MySQL 8.0, it is `utf8mb4`.

You can also specify the `--character-set-server` option when starting `mysqld` to set the `server` level character set.

```bash
mysqld
mysqld --character-set-server=utf8mb4
mysqld --character-set-server=utf8mb4 \
  --collation-server=utf8mb4_0900_ai_ci
```

Alternatively, if you start MySQL using the source build method, you can specify options in the `cmake` command:

```sh
cmake . -DDEFAULT_CHARSET=latin1
or
cmake . -DDEFAULT_CHARSET=latin1 \
  -DDEFAULT_COLLATION=latin1_german1_ci
```

In addition, you can also change the value of `character_set_server` at runtime to modify the `server` level character set.

The `server` level character set is a global setting for the MySQL server. It serves as the default character set when creating or modifying databases (if no other character set is specified) and will also affect the character set used for communication between client and server. For more details, see [MySQL Connector/J 8.0 - 6.7 Using Character Sets and Unicode](https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-reference-charsets.html).

#### database

The `database` level character set is specified when creating and modifying databases:

```sql
CREATE DATABASE db_name
    [[DEFAULT] CHARACTER SET charset_name]
    [[DEFAULT] COLLATE collation_name]

ALTER DATABASE db_name
    [[DEFAULT] CHARACTER SET charset_name]
    [[DEFAULT] COLLATE collation_name]
```

As mentioned before, if the character set is not specified when executing the above statements, MySQL will use the `server` level character set.

You can check the character set of a specific database using the following commands:

```sql
USE db_name;
SELECT @@character_set_database, @@collation_database;
```

```sql
SELECT DEFAULT_CHARACTER_SET_NAME, DEFAULT_COLLATION_NAME
FROM INFORMATION_SCHEMA.SCHEMATA WHERE SCHEMA_NAME = 'db_name';
```

#### table

The `table` level character set is specified when creating and modifying tables:

```sql
CREATE TABLE tbl_name (column_list)
    [[DEFAULT] CHARACTER SET charset_name]
    [COLLATE collation_name]]

ALTER TABLE tbl_name
    [[DEFAULT] CHARACTER SET charset_name]
    [COLLATE collation_name]
```

If no character set is specified when creating or modifying a table, the `database` level character set will be used.

#### column

The `column` level character set is also specified during table creation and modification, but it is defined within the column definition. Here is an example:

```sql
CREATE TABLE t1
(
    col1 VARCHAR(5)
      CHARACTER SET latin1
      COLLATE latin1_german1_ci
);
```

If no character set is specified at the column level, the table-level character set will be used.

### Connection Character Set

Earlier, we discussed the hierarchical levels of character sets, which are related to storage. The connection character set involves communication with the MySQL server.

The connection character set is closely related to the following variables:

- `character_set_client`: Describes which character set is used for SQL statements sent from the client to the server.
- `character_set_connection`: Describes which character set the server uses to translate SQL statements upon receipt.
- `character_set_results`: Describes the character set used for results returned from the server to the client.

The values of these variables can be queried using the following SQL statements:

```sql
SELECT * FROM performance_schema.session_variables
WHERE VARIABLE_NAME IN (
'character_set_client', 'character_set_connection',
'character_set_results', 'collation_connection'
) ORDER BY VARIABLE_NAME;
```

```sql
SHOW SESSION VARIABLES LIKE 'character\_set\_%';
```

If you want to modify the values of the previously mentioned variables, you can do so in the following ways:

1. Modify the configuration file

```properties
[mysql]
# Only for MySQL client programs
default-character-set=utf8mb4
```

2. Use SQL statements

```sql
set names utf8mb4
# Or modify each one individually
# SET character_set_client = utf8mb4;
# SET character_set_results = utf8mb4;
# SET collation_connection = utf8mb4;
```

### JDBC and Connection Character Set

Have you ever encountered a situation where emojis are stored correctly, but when querying with software like Navicat, the emojis appear as question marks? This issue is likely caused by the JDBC driver.

From the previous content, we know that the connection character set also affects the data we store, and the JDBC driver impacts the connection character set.

The `mysql-connector-java` (JDBC driver) mainly affects the connection character set through the following properties:

- `characterEncoding`
- `characterSetResults`

For example, in `DataGrip 2023.1.2`, in the advanced dialog for configuring data sources, the default value for `characterSetResults` is `utf8`. When using `mysql-connector-java 8.0.25`, the connection character set ends up being set to `utf8mb3`. In this case, emojis will be displayed as question marks, and the current version of the driver does not support setting `characterSetResults` to `utf8mb4`. However, switching to `mysql-connector-java driver 8.0.29` allows this.

For specifics, you can read the StackOverflow answer [DataGrip MySQL stores emojis correctly but displays them as?](https://stackoverflow.com/questions/54815419/datagrip-mysql-stores-emojis-correctly-but-displays-them-as).

### Using UTF-8

Generally, we recommend using UTF-8 as the default character encoding method.

However, there is a small pitfall.

MySQL's character encoding includes two implementations of UTF-8 encoding:

- **`utf8`**: The `utf8` encoding supports only `1-3` bytes. In `utf8`, Chinese characters occupy 3 bytes, while numbers, English characters, and symbols occupy 1 byte each. However, emoji characters take up 4 bytes, as do some more complex characters and traditional Chinese characters.
- **`utf8mb4`**: The complete implementation of UTF-8, the "real" deal! It supports up to 4 bytes per character, making it suitable for storing emoji symbols.

**Why are there two implementations of UTF-8?** The reason is as follows:

![](https://oss.javaguide.cn/javaguide/image-20211008164542347.png)

As a result, if you need to store `emoji` data or some more complex characters or traditional Chinese characters in a MySQL database, the database's encoding must be specified as `utf8mb4`, not `utf8`, otherwise, an error will occur during storage.

Let’s demonstrate! (Environment: MySQL 5.7+)

Here is the create table statement, where we specify the database CHARSET as `utf8`.

```sql
CREATE TABLE `user` (
  `id` varchar(66) CHARACTER SET utf8mb3 NOT NULL,
  `name` varchar(33) CHARACTER SET utf8mb3 NOT NULL,
  `phone` varchar(33) CHARACTER SET utf8mb3 DEFAULT NULL,
  `password` varchar(100) CHARACTER SET utf8mb3 DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

When we execute the following insert statement to insert data into the database, an error indeed occurs!

```sql
INSERT INTO `user` (`id`, `name`, `phone`, `password`)
VALUES
 ('A00003', 'guide哥😘😘😘', '181631312312', '123456');
```

The reported error message is as follows:

```plain
Incorrect string value: '\xF0\x9F\x98\x98\xF0\x9F...' for column 'name' at row 1
```

## References

- Character set and character encoding (Charset & Encoding): <https://www.cnblogs.com/skynet/archive/2011/05/03/2035105.html>
- Clarifying character sets and character encoding in ten minutes: <http://cenalulu.github.io/linux/character-encoding/>
- Unicode - Wikipedia: <https://zh.wikipedia.org/wiki/Unicode>
- GB2312 - Wikipedia: <https://zh.wikipedia.org/wiki/GB_2312>
- UTF-8 - Wikipedia: <https://zh.wikipedia.org/wiki/UTF-8>
- GB18030 - Wikipedia: <https://zh.wikipedia.org/wiki/GB_18030>
- MySQL 8 Documentation: <https://dev.mysql.com/doc/refman/8.0/en/charset.html>
- MySQL 5.7 Documentation: <https://dev.mysql.com/doc/refman/5.7/en/charset.html>
- MySQL Connector/J Documentation: <https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-reference-charsets.html>

<!-- @include: @article-footer.snippet.md -->
