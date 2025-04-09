I don't...
title: HashMap Source Analysis
Category:
Tag:
- Java.
I don't...

@include: @article-header.snippet.md--

Thanks to [changefubai](https://github.com/changefubai) for its contribution to the improvement of this paper!

# HashMap Profile

HashMap is mainly used to store key-value pairs. It is based on the Hash table Map interface, it is one of the usual Java collections, and it is non-linear in nature.

`HashMap` can store null keys and values, but null can only have one key; null can have multiple values.

Before JDK 1.8, HashMap consisted of an array of chains, the main body of HashMap, which existed primarily to resolve the Hash conflict (the “Lax method” of conflict resolution). JDK 1.8 introduced significant changes in the resolution of the Hash conflict. When the length of the chain table was greater than or equal to the threshold (default 8), the length of the chain table would be judged before conversion to a red tree. If the length of the current array was less than 64, the choice would be to expand the array first instead of converting to a red tree to reduce the search time.

The default initial size of `HashMap` is 16. Each subsequent time, the capacity becomes twice as large. Furthermore, `HashMap` always uses 2^gills as the size of the Hash watch.

# Bottom Data Structure Analysis

Before JDK 1.8

Before JDK 1.8, the bottom of the HashMap is **array and chain table** combined, i.e., **chain table hash**.

HashMap obtains the Hash value from the HashCode of the key after being processed through the disturbance function and then determines the location of the current element (n here refers to the length of the array) by `(n - 1) & Hash`. If there is an element in the current position, then the element is judged to have the same Hash value as the element to be deposited and whether the key is the same. If so, it is directly overwritten; the same conflict is resolved through the zip method.

The disturbance function refers to the HashMap Hash method. The Hash method is used to prevent some of the worse HashCode() methods; in other words, to reduce collisions with the disturbance function.

**JDK 1.8 HashMap Method Source:**

JDK 1.8's Hash method is simpler than JDK 1.7 Hash, but the principle remains the same.

```Java
Status final int hash(Object key) {
    // key.hashCode(): returns hashcode
    // ^: Differing or
    // >>>: Unsigned Right Move, Ignore Symbols, Empty Places = 0
    (h = key.hashCode()) ^ (h >>> 16);
}
```

Compare the HashMap method source code for JDK 1.7.

```Java
Status hash(int h) {
    // This fight ensures that HashCodes that differ only by
    // the number of settlements.
    h = (h >>> 20) ^ (h >>> 12);
}
```

Compared to the Hash method of JDK 1.8, the Hash method of JDK 1.7 will perform slightly less because of the four disturbances.

The so-called **“Culture method”** is the combination of chains and arrays. This means creating a chain table array, each of which is a chain table. In the event of a Hash conflict, the value of the conflict could be added to the chain table.

Internal structure before [jdk1.8] - HashMap (https://oss.javaguide.cn/github/javaguide/java/collection/jdk1.7_hashmap.png)

# JDK 1.8 After

JDK 1.8 has changed considerably since then in the Hash conflict.

When the length of the chain is greater than the threshold (default 8), the `treebin()` method is first called. This method determines whether to convert to a red tree based on the HashMap array. The conversion of a red tree will only be performed if the array length is greater than or equal to 64 to reduce the search time. Otherwise, it simply implements the `resize()` method for logarithmic enlargement. The relevant source code is closed here. Focus on the `treefulbin()` method!

Internal structure after [jdk1.8] - HashMap (https://oss.javaguide.cn/github/javaguide/java/collection/jdk1.8_hashmap.png)

Properties of category **:**

```Java
public class HashMap<K, V> extends AbstractMap<K, V> implements Map<K, V>, Cloneable, Serializable {
    // Serial number
    private static final long serialVersionUID = 3624898820763181265L;
    // Default initial capacity is 16
    static final int DEFAULT_INITIAL_CAPACITY = 