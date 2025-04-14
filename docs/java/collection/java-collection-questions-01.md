---
title: Summary of Common Java Collection Interview Questions (Part 1)
category: Java
tag:
  - Java Collections
head:
  -   - meta
      - name: keywords
        content: Collection,List,Set,Queue,Deque,PriorityQueue
  -   - meta
      - name: description
        content: A summary of common knowledge points and interview questions about Java collections, hoping to be helpful to you!
---

<!-- @include: @small-advertisement.snippet.md -->

<!-- markdownlint-disable MD024 -->

## Overview of Collections

### Overview of Java Collections

Java collections, also known as containers, are primarily derived from two major interfaces: one is the `Collection` interface, which is mainly used to store single elements; the other is the `Map` interface, which is mainly used to store key-value pairs. For the `Collection` interface, there are three main sub-interfaces: `List`, `Set`, and `Queue`.

The Java collection framework is shown in the diagram below:

![Overview of Java Collection Framework](https://oss.javaguide.cn/github/javaguide/java/collection/java-collection-hierarchy.png)

Note: The diagram only lists the main inheritance relationships and does not enumerate all relationships. For example, it omits abstract classes like `AbstractList`, `NavigableSet`, and other auxiliary classes. For a deeper understanding, you can refer to the source code.

### What are the differences between List, Set, Queue, and Map?

- `List` (a good helper for order): The stored elements are ordered and can be duplicated.
- `Set` (emphasizing uniqueness): The stored elements cannot be duplicated.
- `Queue` (a call machine that implements queuing functionality): Determines the order based on specific queuing rules, and the stored elements are ordered and can be duplicated.
- `Map` (an expert in searching using keys): Stores key-value pairs (key-value), similar to the mathematical function y=f(x), where "x" represents the key and "y" represents the value. Keys are unordered and cannot be duplicated, while values are unordered and can be duplicated, with each key mapping to at most one value.

### Summary of Underlying Data Structures in the Collection Framework

First, let's look at the collections under the `Collection` interface.

#### List

- `ArrayList`: An `Object[]` array. For details, see: [ArrayList Source Code Analysis](./arraylist-source-code.md).
- `Vector`: An `Object[]` array.
- `LinkedList`: A doubly linked list (previously a circular linked list before JDK1.6, removed in JDK1.7). For details, see: [LinkedList Source Code Analysis](./linkedlist-source-code.md).

#### Set

- `HashSet` (unordered, unique): Implemented based on `HashMap`, using `HashMap` to store elements.
- `LinkedHashSet`: A subclass of `HashSet`, implemented internally using `LinkedHashMap`.
- `TreeSet` (ordered, unique): A red-black tree (self-balancing sorted binary tree).

#### Queue

- `PriorityQueue`: Implemented using a min-heap with an `Object[]` array. For details, see: [PriorityQueue Source Code Analysis](./priorityqueue-source-code.md).
- `DelayQueue`: A `PriorityQueue`. For details, see: [DelayQueue Source Code Analysis](./delayqueue-source-code.md).
- `ArrayDeque`: A resizable dynamic array.

Now let's look at the collections under the `Map` interface.

#### Map

- `HashMap`: Before JDK1.8, `HashMap` was composed of an array and a linked list, where the array was the main body and the linked list existed mainly to resolve hash collisions (using the "chaining method" to resolve conflicts). After JDK1.8, there were significant changes in resolving hash collisions. When the length of the linked list exceeds a threshold (default is 8), it converts the linked list to a red-black tree to reduce search time. For details, see: [HashMap Source Code Analysis](./hashmap-source-code.md).
- `LinkedHashMap`: Inherits from `HashMap`, so its underlying structure is still based on a chained hash structure composed of an array and a linked list or red-black tree. Additionally, `LinkedHashMap` adds a doubly linked list to maintain the insertion order of key-value pairs. It also implements access order logic through appropriate operations on the linked list. For details, see: [LinkedHashMap Source Code Analysis](./linkedhashmap-source-code.md).
- `Hashtable`: Composed of an array and a linked list, where the array is the main body and the linked list exists mainly to resolve hash collisions.
- `TreeMap`: A red-black tree (self-balancing sorted binary tree).

### How to Choose a Collection?

We mainly choose the appropriate collection based on the
