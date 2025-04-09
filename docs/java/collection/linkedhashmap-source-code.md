I don't...
title: LinkedHashMap Source Analysis
Category:
Tag:
- Java.
I don't...

# Linked HashMap Profile

`LinkedHashMap` is a grouping provided by Java, which inherits from `HashMap` and maintains a two-way chain on the `HashMap` basis, so that it is:

1. Support will be provided in an orderly and sequential manner throughout the time.
2. Supports the sorting of elements in order of access to the LRU cache tool for encapsulation.
3. Because of the internal use of two-way chains to maintain nodes, the time-consuming efficiency and the number of elements are proportional, and it is much more efficient than HashMap.

The `LinkedHashMap` logical structure, as shown in the figure below, maintains a two-way chain between nodes on the basis of `HashMap`, which links in an orderly manner the nodes, chains, red and black trees, which were scattered over different buckets.

![Linked HashMap Logic Structure](https://oss.javaguide.cn/github/javaguide/java/collection/linkhashmap-structure-overview.png)

# Linked HashMap with Example

## Insert sequences through #

As follows, we add elements to `LinkedHashMap` in order and then go through it.

```Java
HashMap<String, String> map = new LinkedHashMap<>();
map.put("a", "2");
map.put("g", "3");
map.put("r", "1");
map.put("e", "23");

for (Map.Entry<String, String> entry : map.entrySet()) {
    System.out.println(entry.getKey() + ":" + entry.getValue());
}
```

Output:

```Java
a:2
g:3
r:1
e:23
```

As can be seen, the order of `LinkedHashMap` is in the order of insertion, which is not available to `HashMap`.

## In the order of access #

`LinkedHashMap` defines sorting mode `accessOrder` (boolean type, default false), access order is true and insertion order is false.

In order to keep the order of access running, we can use the `LinkedHashMap` constructor method, which introduces the `accessOrder` attribute, and set `accessOrder` to be true, indicating that it has access order.

```Java
LinkedHashMap<Integer, String> map = new LinkedHashMap<>(16, 0.75f, true);
map.put(1, "one");
map.put(2, "two");
map.put(3, "three");
map.put(4, "four");
map.put(5, "five");
/ Access element 2, which will be moved to the end of the chain table
map.get(2);
/ Access element 3, which will be moved to the end of the chain table
map.get(3);
for (Map.Entry<Integer, String> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " : " + entry.getValue());
}
```

Output:

```Java
1: one
4: four
5: five
2: two
3: three
```

As can be seen, the order of `LinkedHashMap` is iterative with the order of access.

# LRU Cache

From the last one we can learn by `LinkedHashMap` that we can create a simple version of LRU (**L**east**R**ecently**U**sed) cache to ensure that the least recently accessed elements are removed when the stored elements exceed the packaging capacity.

![LRU Cache](https://oss.javaguide.cn/github/javaguide/java/collection/lru-cache.png)

This will be achieved along the following lines:

- Inherit from `LinkedHashMap`;
- The `accessOrder` is specified in the constructor method as true so that the element is moved to the end of the chain, where the head element is the least recently visited element;
- Rewrite the `removeEldestEntry` method, which returns a boolean value and informs `LinkedHashMap` whether it is necessary to remove the head element of the chain (cache capacity limited).

```Java
public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int capacity;

    public LRUCache(int capacity) {
        super(16, 0.75f, true);
        this.capacity = capacity;
    }

    /**
     * Revert true when size exceeds capacity, informing LinkedHashMap to remove the oldest cache item (i.e. the first element of the chain)
     */
    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > capacity;
    }
}
```

The test code is as follows. The initial cache capacity is 3 and then four elements are added in order.

```Java
LRUCache<Integer, String> cache = new LRUCache<>(3);
cache.put(1, "