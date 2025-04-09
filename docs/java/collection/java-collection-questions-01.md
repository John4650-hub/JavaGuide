I don't...
Title: Java Roundup of Common Interviews (up)
Category:
Tag:

- Java
  Head:
- Meta
- Name: keywords
  Queue, Deque, Priority Queue
- Meta
- Name: description
  Java gathers common knowledge points and interview summaries, hopefully for you!
  I don't...

@include: @small-advertisement.snippet.md-->

- markdownlint-disable MD024--

# Gather summary

## Java Collective Overview

Java assembly, also known as packaging, was created mainly by two main interfaces: the `Collection` interface, which is used mainly for the storage of single elements, and the `Map` interface, which is used mainly for the storage of key-value pairs. For the `Collection` interface, there are three main sub-interfaces: `List`, `Set`, and `Queue`.

The Java assembly framework is shown in the following figure:

Overview of [Java] assembly framework (https://oss.javaguide.cn/github/javaguide/java/collection/java-collection-hierarchy.png)

Note: The figure only lists the main inheritance spin-offs, not all. For example, abstract categories such as `AbstractList`, `NavigableSet`, and other ancillary categories are omitted.

List, Set, Queue, Map four?

- `List` (good help in the order of dealing with): The stored elements are orderly and repeatable.
- `Set` (focusing on unique characteristics): The elements stored cannot be duplicated.
- `Queue` (the caller for queuing): The sequence is determined by specific queuing rules, and the elements stored are orderly and repeatable.
- `Map` (experts searching with key): Storage using key-value pairs, similar to the mathematical function y=f(x), "x" for key, "y" for value, "key" for value, which is unordered, non-duplicable, value is unordered, repeatable, with a maximum of one value per key.

## Summon bottom data structure summary

Let's look at the `Collection` interface below the assembly.

# List

- `ArrayList`: `Object[]` array. Details are available: [ArrayList Source Analysis](./Arraylist-source-code.md).
- `Vector`: `Object[]` array.
- `LinkedList`: a two-way chain table (JDK 1.6, formerly a loop, JDK 1.7 cancels the cycle). Details can be found at: [LinkedList Source Analysis](./linkedlist-source-code.md).

# Set

- `HashSet` (unordered, unique): Based on `HashMap`, the base uses `HashMap` to save elements.
- `LinkedHashSet`: `LinkedHashSet` is a subcategory of `HashSet`, which is internalized through `LinkedHashMap`.
- `TreeSet` (Ordered, Unique): Red and black trees (a self-balancing sort of binary tree).

# Queue

- `PriorityQueue`: `Object[]` arrays to achieve small peak piles. Details can be found: [PriorityQueue Source Analysis](./priorityQueue-source-code.md).
- `DelayQueue`: `PriorityQueue`. Details can be found at: [DelayQueue Source Analysis](./delayqueue-source-code.md).
- `ArrayDeque`: resizable dynamic two-way arrays.

Let's look again at the `Map` interface under the assembly.

# Map

- `HashMap`: JDK1.8 `HashMap`, consisting of an array of `HashMap`, which is the subject of `HashMap`, which exists primarily for the purpose of resolving the hash conflict (the `Laxation` conflict). JDK1.8 has seen significant changes in the resolution of the hash conflict, when the length of the chain is greater than the threshold (default 8) (the length of the chain is judged before converting it to a red tree, and if the length of the current array is less than 64, the choice is made to expand the array first instead of turning it to a red tree) to reduce the search time. Details are available: [HashMap Source Analysis](./hashmap-source-code.md).
- `LinkedHashMap`: `LinkedHashMap` inherited from `HashMap`, so its bottom is still a zip-based hash structure consisting of arrays and chains or red trees. In addition, `LinkedHashMap` adds a two-way chain table to the above structure, so that the top structure can maintain the order in which the key values are inserted. The logic of the order of access has also been achieved through the corresponding operation of the chain table. See in detail: [LinkedHashMap Source Analysis](./linkedhashmap-source-code.md).
- `Hashtable`: An array of chains consisting of the main body of `Hashtable`, which exists primarily to resolve the hash conflict.
- `TreeMap`: Red and Black Tree (the self-balancing sort of binary tree).

# How to choose the collection?

We choose the right set primarily on the basis of their characteristics. For example:

- We
