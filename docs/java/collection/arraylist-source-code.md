I don't...
type: ArrayList source analysis
Category:
Tag:

- Java.
  I don't...

@include: @small-advertition.snippet.md-->

# ArrayList Introduction

The bottom of `ArrayList` is an array, which corresponds to a dynamic array. Its capacity increases dynamically compared to the arrays in Java. Before adding a large number of elements, the application can increase the capacity of the `ArrayList` example using the `ensureCapacity` operation. This reduces the incremental redistribution.

`ArrayList` succeeded to the `AbstractList` and achieved these `List`, `RandomAccess`, `Cloneable`, and `java.io.Serializable` interfaces.

```Java
public class ArrayList {
    // Implementation details
}
```

- `List`: Shows that it is a list that supports additions, deletions, searches, etc., and can be accessed by subscript.
- `RandomAccess`: This is a marker interface that shows that the `List` collection achieving this interface supports **rapid random access**. In `ArrayList`, we can quickly access the object of the element through its index, which is rapid random access.
- `Cloneable`: Demonstrates that it has a copy capability to perform deep or shallow copy operations.
- `Serializable`: It is convenient to indicate that it can perform a serialized operation, i.e., it can convert objects to bytes for permanent storage or network transmission.

![ArrayList Class Diagram](https://oss.javaguide.cn/github/java/collection/arraylist-class-diagram.png)

The difference between ArrayList and Vector?

- `ArrayList` is the main category of realization for `List` and uses `Object[]` storage at the bottom level for frequent search operations with unsafe threads.
- `Vector` is an older implementation class of `List`, which uses `Object[]` for storage at the bottom and is thread-safe.

# Can ArrayList add a null value?

`ArrayList` can store any type of object, including `null` values. However, it is not recommended to add the `null` value to the `ArrayList`, as it is meaningless and makes it difficult to maintain the code, for example, by forgetting to check for null, leading to a NullPointerException.

Example code:

```Java
ArrayList<String> listOfStrings = new ArrayList<>();
listOfStrings.add(null);
System.out.println(listOfStrings);
```

Output:

```plain
[null]
```

# How is ArrayList different from LinkedList?

- **Thread Safety:** `ArrayList` and `LinkedList` are not synchronized, i.e., not thread-safe.
- **Underlying Data Structure:** `ArrayList` uses an **array**; `LinkedList` uses a **doubly linked list** data structure (JDK1.6 previously used a circular linked list) and JDK1.7 removed the circular structure. Note the difference between a doubly linked list and a circular linked list.
- **Insertion and Deletion Impacted by Element Location:**
  - `ArrayList` uses array storage, so the time complexity of inserting and removing elements is influenced by the location of the elements. For example, when implementing the `add(E)` method, `ArrayList` defaults to adding specified elements to the end of this list, the complexity of which is O(1). However, the time complexity is O(n) if the element is to be inserted or deleted at a specified location (`add(int index, E element)`). This is because the (n-i) elements after the i-th element need to be shifted during the operation.
  - `LinkedList` stores elements in nodes, so the insertion or deletion of elements at the end is not influenced by the position of the elements (`add(E)`, `addFirst(E)`, `addLast(E)`, `removeFirst()`, `removeLast()`), the time complexity is O(1). If the element is to be inserted or deleted at a designated location `i` (`add(int index, E element)`, `remove(Object o)`, `remove(int index)`), the time complexity is O(n) because it is necessary to traverse to the designated location before insertion or deletion.
- **Support for Rapid Random Access:** `LinkedList` does not support efficient random element access, while `ArrayList` (which implements the `RandomAccess` interface) does. Rapid random access is the quick acquisition of element objects by their index (responding to the `get(int index)` method).
- **Memory Space Occupancy:** Space waste in `ArrayList` is mainly reflected in the amount of space reserved at the end of the list, while `LinkedList`'s space cost is reflected in the fact that each of its elements requires more space than `ArrayList` (because of the need to store pointers to the next and previous nodes).

# ArrayList Core Source Interpretation

Using the example of JDK 1.8, an analysis of the bottom source code of `ArrayList` is here
