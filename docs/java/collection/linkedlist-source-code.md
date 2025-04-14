---
title: LinkedList Source Code Analysis
category: Java
tag:
  - Java Collections
---

<!-- @include: @article-header.snippet.md -->

## Introduction to LinkedList

`LinkedList` is a collection class implemented based on a doubly linked list, often compared with `ArrayList`. A detailed comparison between `LinkedList` and `ArrayList` can be found in our [Summary of Common Java Collection Interview Questions (Part 1)](./java-collection-questions-01.md).

![Doubly Linked List](https://oss.javaguide.cn/github/javaguide/cs-basics/data-structure/bidirectional-linkedlist.png)

However, in projects, we generally do not use `LinkedList`. Any scenario that requires `LinkedList` can almost always be replaced with `ArrayList`, and performance is usually better! Even the author of `LinkedList`, Joshua Bloch, has stated that he never uses `LinkedList`.

![](https://oss.javaguide.cn/github/javaguide/redisimage-20220412110853807.png)

Additionally, do not instinctively think that `LinkedList`, as a linked list, is the most suitable for scenarios involving element insertion and deletion. As mentioned above, `LinkedList` only has a time complexity close to O(1) for inserting or deleting elements at the head or tail; in other cases, the average time complexity for insertion and deletion is O(n).

### What is the Time Complexity for Inserting and Deleting Elements in LinkedList?

- Insertion/Deletion at the Head: Only the pointer of the head node needs to be modified to complete the insertion/deletion operation, so the time complexity is O(1).
- Insertion/Deletion at the Tail: Only the pointer of the tail node needs to be modified to complete the insertion/deletion operation, so the time complexity is O(1).
- Insertion/Deletion at a Specified Position: It is necessary to first move to the specified position and then modify the pointer of the specified node to complete the insertion/deletion. However, since there are head and tail pointers, we can start from the closer pointer, so on average, we need to traverse n/4 elements, resulting in a time complexity of O(n).

### Why Can't LinkedList Implement the RandomAccess Interface?

`RandomAccess` is a marker interface that indicates that the classes implementing this interface support random access (i.e., can quickly access elements by index). Since the underlying data structure of `LinkedList` is a linked list, with non-contiguous memory addresses, it can only locate elements through pointers and does not support random quick access, so it cannot implement the `RandomAccess` interface.

## LinkedList Source Code Analysis

Here, we will analyze the core source code of `LinkedList` using JDK 1.8 as an example.

The class definition of `LinkedList` is as follows:

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
{
  //...
}
```

`LinkedList` inherits from `AbstractSequentialList`, which in turn inherits from `AbstractList`.

Having read the source code of `ArrayList`, we know that `ArrayList` also inherits from `AbstractList`, so `LinkedList` will have many similar methods to `ArrayList`.

`LinkedList` implements the following interfaces:

- `List`: Indicates that it is a list, supporting operations such as adding, deleting, and searching, and can be accessed by index.
- `Deque`: Inherited from the `Queue` interface, it has the characteristics of a double-ended queue, supporting insertion and deletion of elements from both ends, making it convenient for implementing data structures like stacks and queues. Note that `Deque` is pronounced "deck" [dɛk], which most people mispronounce.
- `Cloneable`: Indicates that it has copy capabilities, allowing for deep or shallow copy operations.
- `Serializable`: Indicates that it can perform serialization operations, meaning it can convert objects into byte streams for persistent storage or network transmission, which is very convenient.

![LinkedList Class Diagram](https://oss.javaguide.cn/github/javaguide/java/collection/linkedlist--class-diagram.png)

The elements in `LinkedList` are defined through `Node`:

```java
private static class Node<E> {
    E item; // Node value
    Node<E> next; // Pointer to the next node (successor node)
    Node<E> prev; // Pointer to the previous node (predecessor node)

    // The order of initialization parameters is: predecessor node, itself node value, successor node
    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```

### Initialization

`LinkedList` has a no-argument constructor and a
