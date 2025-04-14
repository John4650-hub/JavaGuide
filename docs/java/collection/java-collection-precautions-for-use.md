---
title: Summary of Java Collection Usage Precautions
category: Java
tag:
  - Java Collections
---

In this article, I summarize common precautions and specific principles regarding the use of collections based on the "Alibaba Java Development Manual."

I strongly recommend everyone to read this multiple times to avoid these basic issues when writing code.

## Checking for Empty Collections

The description in the "Alibaba Java Development Manual" is as follows:

> **When checking if all elements within a collection are empty, use the `isEmpty()` method instead of `size() == 0`.**

This is because the `isEmpty()` method is more readable and has a time complexity of `O(1)`.

For most collections we use, the time complexity of the `size()` method is also `O(1)`. However, there are many that are not `O(1)`, such as `ConcurrentLinkedQueue` in the `java.util.concurrent` package. The `isEmpty()` method of `ConcurrentLinkedQueue` checks using the `first()` method, which returns the first node in the queue whose value is not `null` (the reason for node values being `null` is due to logical deletions used in iterators).

```java
public boolean isEmpty() { return first() == null; }

Node<E> first() {
    restartFromHead:
    for (;;) {
        for (Node<E> h = head, p = h, q;;) {
            boolean hasItem = (p.item != null);
            if (hasItem || (q = p.next) == null) {  // Current node's value is not null or reached the end of the queue
                updateHead(h, p);  // Set head to p
                return hasItem ? p : null;
            }
            else if (p == q) continue restartFromHead;
            else p = q;  // p = p.next
        }
    }
}
```

Since the `updateHead(h, p)` method is executed during the insertion and deletion of elements, its time complexity can be approximated to `O(1)`. However, the `size()` method requires traversing the entire linked list, with a time complexity of `O(n)`.

```java
public int size() {
    int count = 0;
    for (Node<E> p = first(); p != null; p = succ(p))
        if (p.item != null)
            if (++count == Integer.MAX_VALUE)
                break;
    return count;
}
```

Additionally, in `ConcurrentHashMap` version 1.7, the time complexities of the `size()` and `isEmpty()` methods are different. `ConcurrentHashMap` version 1.7 stores the element count in each `Segment`, and the `size()` method needs to count each `Segment`, while `isEmpty()` just needs to find the first non-empty `Segment`. However, in `ConcurrentHashMap` version 1.8, both the `size()` and `isEmpty()` methods need to call the `sumCount()` method, which has a time complexity that relates to the size of the `Node` array. Below is the source code for the `sumCount()` method:

```java
final long sumCount() {
    CounterCell[] as = counterCells; CounterCell a;
    long sum = baseCount;
    if (as != null)
        for (int i = 0; i < as.length; ++i)
            if ((a = as[i]) != null)
                sum += a.value;
    return sum;
}
```

This is because in a concurrent environment, `ConcurrentHashMap` stores the count of nodes in each `Node` in a `CounterCell[]` array. In `ConcurrentHashMap` version 1.7, the element count is stored in each `Segment`, and the `size()` method needs to count each `Segment`, while `isEmpty()` only needs to find the first non-empty `Segment`.

## Converting Collection to Map

The description in the "Alibaba Java Development Manual" is as follows:

> **When using the `toMap()` method of the `java.util.stream.Collectors` class to convert to a Map collection, be aware that a NPE exception will be thrown if the value is null.**

```java
class Person {
    private String name;
    private String phoneNumber;
     // getters and setters
}

List<Person> bookList = new ArrayList<>();
bookList.add(new Person("jack","18163138123"));
bookList.add(new Person("martin",null));
// Null pointer exception
bookList.stream().collect(Collectors.toMap(Person::getName, Person::getPhoneNumber));
```

Let's explain the reason.

First, we look at the `toMap()` method of the `java.util.stream.Collectors` class. It internally calls the `merge()` method of the `Map` interface.

```java
public static <T, K, U, M extends Map<K, U>>
Collector<T, ?, M> toMap(Function<? super T, ? extends K> keyMapper,
                            Function<? super T, ? extends U> valueMapper,
                            BinaryOperator<U> mergeFunction,
                            Supplier<M> mapSupplier) {
    BiConsumer<M, T> accumulator
            = (map, element) -> map.merge(keyMapper.apply(element),
                                          valueMapper.apply(element), mergeFunction);
    return new CollectorImpl<>(mapSupplier, accumulator, mapMerger(mergeFunction), CH_ID);
}
```

The `merge()` method of the `Map` interface is as follows. This method is the default implementation in the interface.

> If you are not familiar with the new features of Java 8, please read this article: [Summary of Java 8 New Features](https://mp.weixin.qq.com/s/ojyl7B6PiHaTWADqmUq2rw).

```java
default V merge(K key, V value,
        BiFunction<? super V, ? super V, ? extends V> remappingFunction) {
    Objects.requireNonNull(remappingFunction);
    Objects.requireNonNull(value);
    V oldValue = get(key);
    V newValue = (oldValue == null) ? value :
               remappingFunction.apply(oldValue, value);
    if(newValue == null) {
        remove(key);
    } else {
        put(key, newValue);
    }
    return newValue;
}
```

The `merge()` method first calls the `Objects.requireNonNull()` method to check if the value is null.

```java
public static <T> T requireNonNull(T obj) {
    if (obj == null)
        throw new NullPointerException();
    return obj;
}
```

## Iterating Through Collections

The description in the "Alibaba Java Development Manual" is as follows:

> **Do not perform `remove/add` operations on elements within a foreach loop. To remove elements, use the `Iterator` approach. If concurrent operations are involved, you need to lock the `Iterator` object.**

By decompiling, you will find that the foreach syntax fundamentally relies on `Iterator`. However, the `remove/add` operations directly call the collection's own methods instead of the `Iterator`'s `remove/add` methods.

This leads to the `Iterator` confusingly detecting that some elements have been `remove/add`, which will result in it throwing a `ConcurrentModificationException` to indicate that a concurrent modification exception has occurred. This is the **fail-fast mechanism** that arises in single-threaded contexts.

> **Fail-fast mechanism**: When multiple threads modify a fail-fast collection, a `ConcurrentModificationException` may be thrown. Even in a single-threaded context, such situations can occur, as previously mentioned.
>
> Related reading: [What is fail-fast](https://www.cnblogs.com/54chensongxia/p/12470446.html).

Starting from Java 8, you can use the `Collection#removeIf()` method to remove elements that meet specific conditions, such as:

```java
List<Integer> list = new ArrayList<>();
for (int i = 1; i <= 10; ++i) {
    list.add(i);
}
list.removeIf(filter -> filter % 2 == 0); /* Removes all even numbers from list */
System.out.println(list); /* [1, 3, 5, 7, 9] */
```

In addition to using `Iterator` for traversing, you can also:

- Use a standard for loop
- Use fail-safe collection classes. All collection classes in the `java.util` package are fail-fast, whereas all classes in the `java.util.concurrent` package are fail-safe.
- …

## Removing Duplicates from Collections

The description in the "Alibaba Java Development Manual" is as follows:

> **You can utilize the unique feature of `Set` to quickly remove duplicates from a collection, avoiding the use of `List`'s `contains()` for traversal removal or inclusion checks.**

Here we illustrate with `HashSet` and `ArrayList`.

```java
// Set deduplication code example
public static <T> Set<T> removeDuplicateBySet(List<T> data) {

    if (CollectionUtils.isEmpty(data)) {
        return new HashSet<>();
    }
    return new HashSet<>(data);
}

// List deduplication code example
public static <T> List<T> removeDuplicateByList(List<T> data) {

    if (CollectionUtils.isEmpty(data)) {
        return new ArrayList<>();

    }
    List<T> result = new ArrayList<>(data.size());
    for (T current : data) {
        if (!result.contains(current)) {
            result.add(current);
        }
    }
    return result;
}
```

The core difference between the two lies in the implementation of the `contains()` method.

The `contains()` method of `HashSet` depends on the `containsKey()` method of `HashMap`, with a time complexity close to O(1) (when there are no hash collisions, it is O(1)).

```java
private transient HashMap<E,Object> map;
public boolean contains(Object o) {
    return map.containsKey(o);
}
```

If we insert N elements into the Set, the time complexity approaches O(n).

The `contains()` method of `ArrayList` traverses all elements, with a time complexity close to O(n).

```java
public boolean contains(Object o) {
    return indexOf(o) >= 0;
}
public int indexOf(Object o) {
    if (o == null) {
        for (int i = 0; i < size; i++)
            if (elementData[i]==null)
                return i;
    } else {
        for (int i = 0; i < size; i++)
            if (o.equals(elementData[i]))
                return i;
    }
    return -1;
}
```

## Converting Collection to Array

The description in the "Alibaba Java Development Manual" is as follows:

> **When converting a collection to an array, you must use the collection's `toArray(T[] array)`, passing a completely matching, length-zero empty array.**

The parameter for `toArray(T[] array)` is a generic array. If no parameters are passed to the `toArray` method, it returns an array of type `Object`.

```java
String [] s= new String[]{
    "dog", "lazy", "a", "over", "jumps", "fox", "brown", "quick", "A"
};
List<String> list = Arrays.asList(s);
Collections.reverse(list);
// Will throw an error if type is not specified
s=list.toArray(new String[0]);
```

Due to JVM optimizations, using `new String[0]` as the parameter for `Collection.toArray()` is better now; `new String[0]` acts as a template, specifying the return type of the array, and 0 is for saving space as it is only to indicate the return type. See details: <https://shipilev.net/blog/2016/arrays-wisdom-ancients/>

## Converting Array to Collection

The description in the "Alibaba Java Development Manual" is as follows:

> **When using the utility class `Arrays.asList()` to convert an array into a collection, you should not use its modifying methods; its `add/remove/clear` methods will throw an `UnsupportedOperationException`.**

I encountered a similar pitfall in a previous project.

`Arrays.asList()` is quite common in everyday development, allowing us to convert an array into a `List` collection.

```java
String[] myArray = {"Apple", "Banana", "Orange"};
List<String> myList = Arrays.asList(myArray);
// The above two statements are equivalent to the following one
List<String> myList = Arrays.asList("Apple","Banana", "Orange");
```

The JDK source code describes this method as:

```java
/**
  * Returns a fixed-size list backed by the specified array. This method serves as a bridge between array-based and
  * collection-based APIs, to be used together with Collection.toArray(). The returned List is serializable and implements
  * RandomAccess interface.
  */
public static <T> List<T> asList(T... a) {
    return new ArrayList<>(a);
}
```

Let's summarize the usage precautions.

**1. `Arrays.asList()` is a generic method, and the passed array must be an object array, not a primitive type.**

```java
int[] myArray = {1, 2, 3};
List myList = Arrays.asList(myArray);
System.out.println(myList.size());//1
System.out.println(myList.get(0));//Array address value
System.out.println(myList.get(1));//Error: ArrayIndexOutOfBoundsException
int[] array = (int[]) myList.get(0);
System.out.println(array[0]);//1
```

When a primitive type array is passed, the real parameter obtained by `Arrays.asList()` is the array object itself rather than the elements in the array! At this point, the only element in the `List` is this array, which explains the above code.

Using a wrapper type array can solve this issue.

```java
Integer[] myArray = {1, 2, 3};
```

**2. Using collection modification methods: `add()`, `remove()`, `clear()` will throw exceptions.**

```java
List myList = Arrays.asList(1, 2, 3);
myList.add(4);//Runtime error: UnsupportedOperationException
myList.remove(1);//Runtime error: UnsupportedOperationException
myList.clear();//Runtime error: UnsupportedOperationException
```

The `Arrays.asList()` method does not return a `java.util.ArrayList`, but rather an internal class of `java.util.Arrays`, which does not implement or override the collection modification methods.

```java
List myList = Arrays.asList(1, 2, 3);
System.out.println(myList.getClass());//class java.util.Arrays$ArrayList
```

The following diagram shows a simplified source code of `java.util.Arrays$ArrayList`, and we can see which methods this class overrides.

```java
  private static class ArrayList<E> extends AbstractList<E>
        implements RandomAccess, java.io.Serializable
    {
        ...

        @Override
        public E get(int index) {
          ...
        }

        @Override
        public E set(int index, E element) {
          ...
        }

        @Override
        public int indexOf(Object o) {
          ...
        }

        @Override
        public boolean contains(Object o) {
           ...
        }

        @Override
        public void forEach(Consumer<? super E> action) {
          ...
        }

        @Override
        public void replaceAll(UnaryOperator<E> operator) {
          ...
        }

        @Override
        public void sort(Comparator<? super E> c) {
          ...
        }
    }
```

Now looking at the `add/remove/clear` methods of `java.util.AbstractList` will explain why `UnsupportedOperationException` is thrown.

```java
public E remove(int index) {
    throw new UnsupportedOperationException();
}
public boolean add(E e) {
    add(size(), e);
    return true;
}
public void add(int index, E element) {
    throw new UnsupportedOperationException();
}

public void clear() {
    removeRange(0, size());
}
protected void removeRange(int fromIndex, int toIndex) {
    ListIterator<E> it = listIterator(fromIndex);
    for (int i=0, n=toIndex-fromIndex; i<n; i++) {
        it.next();
        it.remove();
    }
}
```

**So how can we correctly convert an array to an `ArrayList`?**

1. Manually implement a utility class

```java
// JDK1.5+
static <T> List<T> arrayToList(final T[] array) {
  final List<T> l = new ArrayList<T>(array.length);

  for (final T s : array) {
    l.add(s);
  }
  return l;
}


Integer[] myArray = { 1, 2, 3 };
System.out.println(arrayToList(myArray).getClass());//class java.util.ArrayList
```

2. The simplest method

```java
List list = new ArrayList<>(Arrays.asList("a", "b", "c"))
```

3. Using Java 8's `Stream` (recommended)

```java
Integer[] myArray = { 1, 2, 3 };
List myList = Arrays.stream(myArray).collect(Collectors.toList());
// Primitive types can also be converted (depending on boxing operations)
int[] myArray2 = { 1, 2, 3 };
List myList = Arrays.stream(myArray2).boxed().collect(Collectors.toList());
```

4. Using Guava

For immutable collections, you can use the [`ImmutableList`](https://github.com/google/guava/blob/master/guava/src/com/google/common/collect/ImmutableList.java) class and its [`of()`](https://github.com/google/guava/blob/master/guava/src/com/google/common/collect/ImmutableList.java#L101) and [`copyOf()`](https://github.com/google/guava/blob/master/guava/src/com/google/common/collect/ImmutableList.java#L225) factory methods (parameters cannot be null):

```java
List<String> il = ImmutableList.of("string", "elements");  // from varargs
List<String> il = ImmutableList.copyOf(aStringArray);      // from array
```

For mutable collections, you can use the [`Lists`](https://github.com/google/guava/blob/master/guava/src/com/google/common/collect/Lists.java) class and its [`newArrayList()`](https://github.com/google/guava/blob/master/guava/src/com/google/common/collect/Lists.java#L87) factory method:

```java
List<String> l1 = Lists.newArrayList(anotherListOrCollection);    // from collection
List<String> l2 = Lists.newArrayList(aStringArray);               // from array
List<String> l3 = Lists.newArrayList("or", "string", "elements"); // from varargs
```

5. Using Apache Commons Collections

```java
List<String> list = new ArrayList<String>();
CollectionUtils.addAll(list, str);
```

6. Using Java 9's `List.of()` method

```java
Integer[] array = {1, 2, 3};
List<Integer> list = List.of(array);
```

<!-- @include: @article-footer.snippet.md -->
