---
title: ArrayList Source Code Analysis
category: Java
tag:
  - Java Collections
---

<!-- @include: @small-advertisement.snippet.md -->

## Introduction to ArrayList

The underlying structure of `ArrayList` is an array queue, which is equivalent to a dynamic array. Compared to arrays in Java, its capacity can grow dynamically. Before adding a large number of elements, applications can use the `ensureCapacity` operation to increase the capacity of the `ArrayList` instance. This can reduce the number of incremental reallocations.

`ArrayList` inherits from `AbstractList` and implements the interfaces `List`, `RandomAccess`, `Cloneable`, and `java.io.Serializable`.

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
}
```

- `List`: Indicates that it is a list that supports operations such as adding, deleting, and searching, and can be accessed by index.
- `RandomAccess`: This is a marker interface indicating that the `List` collection implementing this interface supports **fast random access**. In `ArrayList`, we can quickly retrieve an element object by its index, which is fast random access.
- `Cloneable`: Indicates that it has the ability to be copied, allowing for deep or shallow copy operations.
- `Serializable`: Indicates that it can be serialized, meaning it can convert objects into byte streams for persistent storage or network transmission, which is very convenient.

![ArrayList Class Diagram](https://oss.javaguide.cn/github/javaguide/java/collection/arraylist-class-diagram.png)

### What is the difference between ArrayList and Vector? (For understanding only)

- `ArrayList` is the main implementation class of `List`, using `Object[]` for storage, suitable for frequent lookup operations, and is not thread-safe.
- `Vector` is an older implementation class of `List`, also using `Object[]` for storage, and is thread-safe.

### Can ArrayList add null values?

`ArrayList` can store any type of object, including `null` values. However, it is not recommended to add `null` values to `ArrayList`, as `null` values are meaningless and can make the code difficult to maintain. For example, forgetting to handle null checks can lead to a NullPointerException.

Example code:

```java
ArrayList<String> listOfStrings = new ArrayList<>();
listOfStrings.add(null);
listOfStrings.add("java");
System.out.println(listOfStrings);
```

Output:

```plain
[null, java]
```

### What is the difference between ArrayList and LinkedList?

- **Thread Safety:** Both `ArrayList` and `LinkedList` are unsynchronized, meaning they do not guarantee thread safety.
- **Underlying Data Structure:** `ArrayList` uses an **`Object` array** for storage; `LinkedList` uses a **doubly linked list** data structure (before JDK1.6 it was a circular linked list, which was removed in JDK1.7. Note the difference between a doubly linked list and a doubly circular linked list, which is introduced below!).
- **Impact of Element Position on Insertion and Deletion:**
  - `ArrayList` uses an array for storage, so the time complexity for inserting and deleting elements is affected by the position of the elements. For example, when executing the `add(E e)` method, `ArrayList` defaults to appending the specified element to the end of the list, which has a time complexity of O(1). However, if you want to insert or delete an element at a specified position `i` (`add(int index, E element)`), the time complexity becomes O(n) because the elements from index `i` and the subsequent (n-i) elements need to be shifted.
  - `LinkedList` uses a linked list for storage, so inserting or deleting elements at the head or tail is not affected by the position of the elements (`add(E e)`, `addFirst(E e)`, `addLast(E e)`, `removeFirst()`, `removeLast()`), with a time complexity of O(1). However, if you want to insert or delete elements at a specified position `i` (`add(int index, E element)`, `remove(Object o)`, `remove(int index)`), the time complexity is O(n) because you need to move to the specified position first before inserting or deleting.
- **Support for Fast Random Access:** `LinkedList` does not support efficient random access to elements, while `ArrayList` (which implements the `RandomAccess` interface) does. Fast random access means quickly retrieving an element object by its index (corresponding to the `get(int index)` method).
- **Memory Space Usage:** The space waste of `ArrayList` mainly lies in reserving a certain capacity at the end of the list,
