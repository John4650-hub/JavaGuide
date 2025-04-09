I don't...
type: LinkedList source analysis
Category:
Tag:

- Java.
  I don't...

@include: @article-header.snippet.md--

# LinkedList Profile

`LinkedList` is a grouping based on a two-way chain and is often compared to `ArrayList`. For a detailed comparison of `LinkedList` and `ArrayList`, we [gather a summary of common interviews](./java-collection-questions-01.md) that are detailed.

![Two-way chain table](https://oss.javaguide.cn/github/javaguide/cs-basics/data-training/bidirectional-linkedlist.png)

However, we do not normally use `LinkedList` in projects and need to use `LinkedList` for almost all of the `ArrayList` scenarios, and performance is usually better! Even Josh Bloch, the author of `LinkedList` himself, stated that he would never use `LinkedList`.

![Image](https://oss.javaguide.cn/github/javaguide/redisimage-2022411085387.png)

In addition, do not consciously think that `LinkedList` as a chain table is the most suitable scenario for element additions and deletions. As I also said above, `LinkedList` is close to O(1) in time only when the element is inserted or deleted at the head or the end, and the average time complexity of adding or deleting elements in other cases is O(n).

# LinkedList: The Time Complexity of Inserting and Removing Elements

- Head insertion/deletion: The insertion/deletion operation can only be completed by modifying the pointer of the node, so the time complexity is O(1).
- End insertion/deletion: The insertion/deletion operation can only be completed by modifying the pointer at the end, so the time complexity is O(1).
- Assign location inserts/deletes: The pointer of the given node needs to be moved to the given position and then modified to be inserted/deleted. Although with a head end pointer, it can be derived from a closer pointer, thus requiring an average of n/4 elements with an O(n) time complexity.

# LinkedList: Why can't you get the Random Access interface?

`RandomAccess` is a tag interface that is used to indicate that the type of access that achieves the interface supports random access (i.e., rapid access to elements by index). The `RandomAccess` interface could not be achieved because the `LinkedList` bottom data structure is a chain table; the memory addresses are not continuous and can only be located through a pointer, and random and fast access is not supported.

# LinkedList source analysis

The bottom core source code of `LinkedList` is analyzed here for example in JDK 1.8.

The category `LinkedList` is defined as follows:

```Java
public class LinkedList {
    // Implementation details
}
```

`LinkedList` extends `AbstractSequentialList`, while `AbstractSequentialList` also extends `AbstractList`.

We know from reading the source code of `ArrayList` that `ArrayList` has also inherited `AbstractList`, so that `LinkedList` will have much of a similar approach to `ArrayList`.

`LinkedList` has the following interfaces:

- `List`: Shows that it is a list that supports additions, deletions, searches, etc., and can be accessed by subscript.
- `Deque`: Inherits from the `Queue` interface, with dual-end Queue features, supports the insertion and removal of elements from both ends, facilitating the realization of data structures such as stacks and queues. It needs to be noted that the `Deque` pronunciation is "deck" [dɛk], which most people read incorrectly.
- `Cloneable`: Demonstrates that it has a copy capability to perform deep or shallow copy operations.
- `Serializable`: It is convenient to indicate that it can perform a sequenced operation, i.e., it can convert objects to bytes for permanent storage or network transmission.

![LinkedList-type diagram](https://oss.javaguide.cn/github/java/collection/linkedlist-class-diagram.png)

Elements in `LinkedList` are defined by `Node`:

```Java
class Node<E> {
    E item; // Node values
    Node<E> next; // Next node
    Node<E> prev; // Previous node

    // The order of the initialization parameters is: the previous node, its own node value, and the next node.
    Node(Node<E> prev, E item, Node<E> next) {
        this.item = item;
        this.next = next;
        this.prev = prev;
    }
}
```

# Initialize

`LinkedList` has a non-parameterized constructor and a parameterized constructor.

```Java
// Create an empty chain watch object
public LinkedList() {
    // Implementation details
}

// Receive a collection type as a parameter that creates a chain table object with the same elements as the input collection
public LinkedList(Collection<? extends E> c) {
```
