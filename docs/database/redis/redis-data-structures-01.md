I don't...
type: Redis 5
Category: Database
Tag:

- Redis
  Head:
- Meta
- Name: keywords
  Contact: Redis Common Data Type
- Meta
- Name: description
  Grant: Redis basic data type summary: String (string), List (list), Set (set), Hash (hash), Zset (ordered collection)
  I don't...

Redis has five basic data types: String (string), List (list), Set (set), Hash (hash), Zset (ordered collection).

These 5 data types are directly available to users and are in the form in which the data is saved, and the bottom level is achieved by relying primarily on these 8 data structures: simple dynamic strings (SDS), LinkedList (bi-directional list), Dict (Hash Table/Dictionary), SkipList (jumplist), Intset (integer collection), ZipList (compressed list), QuickList (quick list).

The bottom structure of the Redis 5 basic data types achieves the following:

"String List, Hash, Set, Zset,
|:\----------------------------------------------------------------------------
SDS |LinkedList/ZipList/QuickList

Before Redis 3.2, the bottom of the List was LinkedList or ZipList. After Redis 3.2, the bottom of the LinkedList and ZipList combined into QuickList, and List became QuickList. Starting with Redis 7.0, ZipList was replaced by ListPack.

You can find a very detailed description of the Redis data type/structure on the Redis website:

- [Redis Data Structures](https://redis.com/redis-entry/data-stream/)
- [Redis Data types tutorial](https://redis.io/docs/manual/data-types/data-types-tutorial/)

In the future, with the release of the new Redis version, there may be a new data structure, and you can always get the most scalable information by looking at the Redis official website.

(https://oss.javaguide.cn/github/javaguide/database/redis/image20207181303.png)

# String (string)

# Introduction

String is the simplest and most common type of data in Redis.

String is a binary secure data type that can be used to store data of any type, such as strings, integers, floating point numbers, images (base64 encoded images or decoded images), and sequenced objects.

(https://oss.javaguide.cn/github/javaguide/database/redis/image20207191243897.png)

Although Redis is written in C, Redis does not use a C string, but rather constructs a **simple dynamic string** (Simple Dynamic String, **SDS**). In addition to the original C string, the SDS of Redis saves binary data, not just text data, and acquires a string length complexity of O(1) (C string O(N)). The SDS API of Redis is safe and does not cause a buffer overflow.

Common commands

| Command | Introduction |
| \- - - - - - - - | - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
