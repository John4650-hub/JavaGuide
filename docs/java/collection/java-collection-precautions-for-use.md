I don't...
Title: Java Pool Usage Summary
Category:
Tag:
- Java.
I don't...

This article is based on the Alibaba Java Development Manual, which summarizes the common concerns about collective use and its specific rationale.

Small partners are strongly advised to read more than once to avoid these low-level problems when they write their own codes.

# Gathering to clear #

The Alibaba Java Development Manual describes it as follows:

> **To determine whether all the elements within the assembly are empty, use the `isEmpty()` method instead of the `size() == 0` method.**

This is because the `isEmpty()` method is better readable and its time complexity is `O(1)`.

Most of the `size()` methods we use are also `O(1)`, but many are not `O(1)`, such as `ConcurrentLinkedQueue` under the `java.util.concurrent`. The `isEmpty()` method of `ConcurrentLinkedQueue` is judged by the `first()` method, in which the `first()` method returns the first node in the queue that is not `null` (node value is `null` because of the logical deletion used in an iterative device).

```Java
_Other Organiser

Node<E> first() {
    RestartFromHead:
    for (;;) {
        for (Node<E> h = head, p = h, q; (p.item != full);) {
            if (hasItem || p.next) { // Current node value is not empty or reaches end of line
                updateHead(h, p); // set head to p
                I don't know.
                ♪ I'm sorry ♪
            }
            if (p == q) {
                p = p.next;
                ♪ I'm sorry ♪
            }
            ♪ I'm sorry ♪
            ♪ I'm sorry ♪
        }
    }
}
```

Since the `updateHead(h, p)` method is implemented when elements are inserted and deleted, the time complexity of its implementation can be similar to `O(1)`. And the `size()` method requires a full chain, with a time complexity of `O(n)`.

```Java
public int size() {
    int count = 0;
    for (Node<E> p = first; p != null; p = subcc(p)) {
        if (p.item != null) {
            if (++count == Integer.MAX_VALUE) {
                I don't know.
            }
        }
    }
    return count;
    ♪ I'm sorry ♪
}
```

In addition, the time complexity of the `size()` method and `isEmpty()` methods in `ConcurrentHashMap` 1.7 is not the same. `ConcurrentHashMap` 1.7 stores the number of elements in each `Segment`, and `size()` methods need to measure the amount of each `Segment`, while `isEmpty()` requires only the first `Segment` that is not empty. However, the `size()` method in `ConcurrentHashMap` 1.8 and `isEmpty()` both require a call to the `sumCount()` method, the complexity of which relates to the size of the `Node` array. The following is the source code of the `sumCount()` method:

```Java
final long sumCount() {
    CounterCell[] as = counterCells; 
    CounterCell a;
    long sum = baseCount;
    if (as != null) {
        for (int i = 0; i < as.length; i++) {
            if ((a = as[i]) != null) {
                sum += a.value;
            }
        }
    }
    return sum;
    ♪ I'm sorry ♪
}
```

This is because `ConcurrentHashMap` stores the number of nodes per `Node` in the `CounterCell[]` array in a simultaneous environment. In `ConcurrentHashMap` 1.7, the quantity of elements is stored in each `Segment`, and `size()` methods need to measure the amount of each `Segment`, while `isEmpty()` requires only the first `Segment` that is not empty.

# Collapse Map

The Alibaba Java Development Manual describes it as follows:

> **When using the `toMap()` method of the `java.util.stream.Collectors` category, care must be taken that NPE anomalies are dropped when the value is null.**

```Java
class Person {
    private String name;
    // Getters and setters
}

List<Person> bookList = new ArrayList<>();
bookList.add(new Person("jack", "18163138123"));
bookList.add(new Person("Martin", null)); // Empty pointer anomaly
bookList.stream().collect(Collectors.toMap(Person::getName, Person::getPhoneNumber));
```

Let's explain why.

First, when we look at the `toMap()` methodology of the `java.util.stream.Collectors` category, we can see the `merge()` method of internal transfer of the `Map` interface.

```Java
public static <T, U> Collector<T, ?, Map