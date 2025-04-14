---
title: LinkedHashMap Source Code Analysis
category: Java
tag:
  - Java Collections
---

## Introduction to LinkedHashMap

`LinkedHashMap` is a collection class provided by Java that extends `HashMap` and maintains a doubly linked list on top of `HashMap`, giving it the following characteristics:

1. Supports ordered iteration according to the insertion order.
2. Supports ordering by access order, suitable for encapsulating LRU cache tools.
3. Because it uses a doubly linked list to maintain nodes, the efficiency of traversal is proportional to the number of elements, making iteration much more efficient compared to `HashMap`, which is proportional to capacity.

The logical structure of `LinkedHashMap` is shown in the diagram below. It maintains a doubly linked list between each node on top of `HashMap`, allowing nodes, linked lists, and red-black trees that are originally hashed into different buckets to be associated in an ordered manner.

![LinkedHashMap Logical Structure](https://oss.javaguide.cn/github/javaguide/java/collection/linkhashmap-structure-overview.png)

## Example of Using LinkedHashMap

### Iteration in Insertion Order

As shown below, we add elements to `LinkedHashMap` in order and then iterate through them.

```java
HashMap < String, String > map = new LinkedHashMap < > ();
map.put("a", "2");
map.put("g", "3");
map.put("r", "1");
map.put("e", "23");

for (Map.Entry < String, String > entry: map.entrySet()) {
    System.out.println(entry.getKey() + ":" + entry.getValue());
}
```

Output:

```java
a:2
g:3
r:1
e:23
```

It can be seen that the iteration order of `LinkedHashMap` is consistent with the insertion order, which is not the case with `HashMap`.

### Iteration in Access Order

`LinkedHashMap` defines a sorting mode `accessOrder` (boolean type, default is false). When `accessOrder` is true, the order is based on access; when false, it is based on insertion.

To achieve iteration in access order, we can use the `LinkedHashMap` constructor that takes the `accessOrder` parameter and set `accessOrder` to true, indicating that it has access order.

```java
LinkedHashMap<Integer, String> map = new LinkedHashMap<>(16, 0.75f, true);
map.put(1, "one");
map.put(2, "two");
map.put(3, "three");
map.put(4, "four");
map.put(5, "five");
// Access element 2, this element will be moved to the end of the list
map.get(2);
// Access element 3, this element will be moved to the end of the list
map.get(3);
for (Map.Entry<Integer, String> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " : " + entry.getValue());
}
```

Output:

```java
1 : one
4 : four
5 : five
2 : two
3 : three
```

It can be seen that the iteration order of `LinkedHashMap` is consistent with the access order.

### LRU Cache

From the previous example, we can see that we can encapsulate a simple LRU (Least Recently Used) cache using `LinkedHashMap`, ensuring that when the stored elements exceed the container capacity, the least recently accessed elements are removed.

![](https://oss.javaguide.cn/github/javaguide/java/collection/lru-cache.png)

The specific implementation idea is as follows:

- Inherit from `LinkedHashMap`;
- In the constructor, set `accessOrder` to true, so that when accessing elements, the element will be moved to the end of the list, and the first element of the list will be the least recently accessed;
- Override the `removeEldestEntry` method, which returns a boolean value to inform `LinkedHashMap` whether to remove the first element of the list (the cache capacity is limited).

```java
public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int capacity;

    public LRUCache(int capacity) {
        super(capacity, 0.75f, true);
        this.capacity = capacity;
    }

    /**
     * Returns true if size exceeds capacity, informing LinkedHashMap to remove the oldest cache entry (i.e., the first element of the list)
     */
    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > capacity;
    }
}
```

The test code is as follows. The author initializes the cache capacity to 3 and then adds 4 elements in order.

```java
LRUCache<Integer,