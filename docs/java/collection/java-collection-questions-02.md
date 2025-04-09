I don't...
Title: Java Roundup of Common Interviews (under)
Category:
Tag:

- Java
  Head:
- Meta
- Name: keywords
  "HashMap, ConcurrentHashMap, Hashtable, List, Set"
- Meta
- Name: description
  Java gathers common knowledge points and interview summaries, hopefully for you!
  I don't...

@include: @article-header.snippet.md--

# Map (important)

The difference between HashMap and Hashtable

- **Thread safety:** `HashMap` is not thread-safe, while `Hashtable` is thread-safe, as `Hashtable` internal methods are largely modified by `synchronized`. (If you want to keep it thread-safe, use `ConcurrentHashMap`!)
- **Efficiency:** Because of thread safety issues, `HashMap` is a little more efficient than `Hashtable`. In addition, `Hashtable` has been largely eliminated from use in code.
- **Support for Null Key and Null Value:** `HashMap` can store null keys and values, but `Hashtable` can only have one null key and can have more than one value; `Hashtable` does not allow null keys and null values; otherwise, a `NullPointerException` will be thrown.
- **The initial capacity size is different from the size of each buildup:** The default initial size of `Hashtable` is 11 if the initial capacity is not specified at the time of creation, and each subsequent buildup becomes the original 2n+1. The default initial size of `HashMap` is 16. Each subsequent time, the capacity becomes twice as large. `Hashtable` will use your given size directly if the initial capacity value is given at the time of creation, and `HashMap` will expand it to a power of two size (`tableSizeFor()` in `HashMap`, the source code is given below). This means that `HashMap` always uses powers of two as the size of the hash table, which is then presented as why powers of two are used.
- **Bottom-level data structure:** In JDK 1.8, `HashMap` changed significantly in the resolution of hash conflicts, when the chain length was greater than the threshold (by default 8), turning the chain into a red-black tree (presuming that if the length of the current array was less than 64, then the choice would be to expand the array first rather than convert it to a red-black tree) in order to reduce the search time (later I will analyze the process in conjunction with the source code). There is no such mechanism in earlier versions.
- **Accomplishment of the Hash function:** `HashMap` was treated with high- and low-level mixed disturbances to reduce conflicts, while `Hashtable` used the `hashCode()` value with the key.

**Constructive function with initial capacity in `HashMap`:**

```Java
public HashMap(int initialCapacity, float loadFactor) {
    if (initialCapacity < 0) 
        throw new IllegalArgumentException("Illegal Capacity: " + initialCapacity);
    if (initialCapacity > MAXIMUM_CAPACITY) 
        initialCapacity = MAXIMUM_CAPACITY;
    if (loadFactor <= 0 || Float.isNaN(loadFactor)) 
        throw new IllegalArgumentException("Illegal load factor: " + loadFactor);
    this.loadFactor = loadFactor;
    this.threshold = tableSizeFor(initialCapacity);
}

public HashMap(int initialCapacity) {
    this(initialCapacity, DEFAULT_LOAD_FACTOR);
}
```

The following method ensures that `HashMap` always uses powers of two as the size of the hash table.

```Java
/**
 * Returns a power of two size for the given target capacity.
 */
static final int tableSizeFor(int cap) {
    int n = cap - 1;
    n |= n >> 1;
    n |= n >> 2;
    n |= n >> 4;
    n |= n >> 8;
    n |= n >> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```

# HashMap and HashSet

If you look at the `HashSet` source code, you should know that the `HashSet` is based on `HashMap`. (The source code for `HashSet` is very small, as the other methods are direct calls to the methods in `HashMap` except `clone()`, `writeObject()`, `readObject()`.)

`HashMap` vs `HashSet`
|:\------------------|:------------------|
| `Map` interface implemented | Store key-value pairs vs. Store objects only |
| Call `put()` to add elements to Map | Call `add()` to add elements to Set |
| `HashMap` uses keys to calculate `hashCode` | `HashSet` calculates the `hashCode` value using the member object. `hashCode` may be the same for both objects, so \`
