---
title: Detailed Explanation of ThreadLocal
category: Java
tag:
  - Java Concurrency
---

> This article is a romantic submission from a branch of flowers. The original address: [https://juejin.cn/post/6844904151567040519](https://juejin.cn/post/6844904151567040519).

### Introduction

![](./images/thread-local/1.png)

**The full text contains over 10,000 words and 31 images. This article also took a considerable amount of time and effort to complete. Original works are not easy, so please show some support by liking and following. Thank you.**

When it comes to `ThreadLocal`, everyone's first reaction might be that it's a simple thread-local variable copy that isolates each thread. However, here are a few questions for you to ponder:

- The `key` of `ThreadLocal` is a **weak reference**. When calling `ThreadLocal.get()`, does the key become **null** after a **GC**?
- What are the **data structures** in `ThreadLocal`'s `ThreadLocalMap`?
- What is the **hash algorithm** of `ThreadLocalMap`?
- How does `ThreadLocalMap` resolve **hash collisions**?
- What is the **expansion mechanism** of `ThreadLocalMap`?
- What is the **cleanup mechanism** for expired keys in `ThreadLocalMap`? What are the processes of **probe cleanup** and **heuristic cleanup**?
- What is the implementation principle of the `ThreadLocalMap.set()` method?
- What is the implementation principle of the `ThreadLocalMap.get()` method?
- How is `ThreadLocal` used in projects? What pitfalls have been encountered?
- ...

Have you mastered the above questions? This article will analyze every detail of `ThreadLocal` using images and text based on these questions.

### Table of Contents

**Note:** The source code in this article is based on `JDK 1.8`.

### `ThreadLocal` Code Demonstration

Let's first look at an example of using `ThreadLocal`:

```java
public class ThreadLocalTest {
    private List<String> messages = Lists.newArrayList();

    public static final ThreadLocal<ThreadLocalTest> holder = ThreadLocal.withInitial(ThreadLocalTest::new);

    public static void add(String message) {
        holder.get().messages.add(message);
    }

    public static List<String> clear() {
        List<String> messages = holder.get().messages;
        holder.remove();

        System.out.println("size: " + holder.get().messages.size());
        return messages;
    }

    public static void main(String[] args) {
        ThreadLocalTest.add("Does a branch of flowers count as romantic?");
        System.out.println(holder.get().messages);
        ThreadLocalTest.clear();
    }
}
```

Output:

```java
[Does a branch of flowers count as romantic?]
size: 0
```

The `ThreadLocal` object provides thread-local variables, where each `Thread` has its own **copy variable**, and multiple threads do not interfere with each other.

### Data Structure of `ThreadLocal`

![](./images/thread-local/2.png)

The `Thread` class has an instance variable `threadLocals` of type `ThreadLocal.ThreadLocalMap`, which means each thread has its own `ThreadLocalMap`.

`ThreadLocalMap` has its own independent implementation, where its `key` can simply be viewed as `ThreadLocal`, and `value` is the value placed in the code (in reality, the `key` is not the `ThreadLocal` itself, but a **weak reference** to it).

Whenever a thread places a value into `ThreadLocal`, it stores it in its own `ThreadLocalMap`, and reading is also done by using `ThreadLocal` as a reference to find the corresponding `key` in its own `map`, thereby achieving **thread isolation**.

`ThreadLocalMap` is somewhat similar to the structure of `HashMap`, except that `HashMap` is implemented using **arrays + linked lists**, while `ThreadLocalMap` does not have a **linked list** structure.

We also need to pay attention to `Entry`, where its `key` is `ThreadLocal<?> k`, inheriting from `WeakReference`, which is what we commonly refer to as weak reference types.

### Is the key null after GC?

To address the question at the beginning, the `key` of `ThreadLocal` is a weak reference. So, when calling `ThreadLocal.get()`, is the `key` **null** after GC?

To clarify this, we need to understand Java's **four types of reference**:

- **Strong Reference**: The objects we usually create with `new` are strong reference types. As long as the strong reference exists, the garbage collector will never reclaim the referenced object, even during memory shortage.
- **Soft Reference**: Objects referenced by `SoftReference` are considered soft references. These objects are collected when there is a risk of memory overflow.
- **Weak Reference**: Objects referenced by `WeakReference` are weak references. As soon as garbage collection occurs, if this object is only pointed to by a weak reference, it will be reclaimed.
- **Phantom Reference**: Phantom references are the weakest references, defined in Java using `PhantomReference`. The only function of a phantom reference is to receive notifications that the object is about to be reclaimed.

Next, let's look at the code. We will use reflection to check the data state in `ThreadLocal` after GC (the following code comes from: <https://blog.csdn.net/thewindkee/article/details/103726942> local running demonstration of GC scenarios):

```java
public class ThreadLocalDemo {

    public static void main(String[] args) throws NoSuchFieldException, IllegalAccessException, InterruptedException {
        Thread t = new Thread(() -> test("abc", false));
        t.start();
        t.join();
        System.out.println("--after gc--");
        Thread t2 = new Thread(() -> test("def", true));
        t2.start();
        t2.join();
    }

    private static void test(String s, boolean isGC) {
        try {
            new ThreadLocal<>().set(s);
            if (isGC) {
                System.gc();
            }
            Thread t = Thread.currentThread();
            Class<? extends Thread> clz = t.getClass();
            Field field = clz.getDeclaredField("threadLocals");
            field.setAccessible(true);
            Object ThreadLocalMap = field.get(t);
            Class<?> tlmClass = ThreadLocalMap.getClass();
            Field tableField = tlmClass.getDeclaredField("table");
            tableField.setAccessible(true);
            Object[] arr = (Object[]) tableField.get(ThreadLocalMap);
            for (Object o : arr) {
                if (o != null) {
                    Class<?> entryClass = o.getClass();
                    Field valueField = entryClass.getDeclaredField("value");
                    Field referenceField = entryClass.getSuperclass().getSuperclass().getDeclaredField("referent");
                    valueField.setAccessible(true);
                    referenceField.setAccessible(true);
                    System.out.println(String.format("Weak reference key: %s, value: %s", referenceField.get(o), valueField.get(o)));
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

The result is as follows:

```java
Weak reference key: java.lang.ThreadLocal@433619b6, value: abc
Weak reference key: java.lang.ThreadLocal@418a15e3, value: java.lang.ref.SoftReference@bf97a12
--after gc--
Weak reference key: null, value: def
```

![](./images/thread-local/3.png)

As shown in the figure, because the `ThreadLocal` created here does not point to any value, i.e., there are no references:

```java
new ThreadLocal<>().set(s);
```

So, after GC, the `key` will be reclaimed. We see from the above `debug` that `referent=null`. If we **modify the code** slightly:

![](./images/thread-local/4.png)

Initially looking at this problem, if you don’t think too much about it, with **weak reference** and **garbage collection**, you may definitely feel it should be `null`.

However, this is incorrect, because the scenario describes that the `ThreadLocal.get()` operation is being performed, which indicates that a **strong reference** is still present, hence the `key` is not `null`, as shown in the following figure, the **strong reference** of `ThreadLocal` is still present.

![](./images/thread-local/5.png)

If our **strong reference** does not exist, then the `key` will be reclaimed, which leads to `value` never being reclaimed, and the `key` being reclaimed results in memory leakage.

### Detailed Explanation of the `ThreadLocal.set()` Method Source Code

![](./images/thread-local/6.png)

The principle of the `set` method in `ThreadLocal` is depicted in the diagram above. It’s quite simple: it mainly checks if `ThreadLocalMap` exists and then uses the `set` method in `ThreadLocal` to process the data.

The code is as follows:

```java
public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
}

void createMap(Thread t, T firstValue) {
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}
```

The key logic is still in `ThreadLocalMap`, which we will analyze in detail later.

### Hash Algorithm of `ThreadLocalMap`

Since it's a `Map` structure, `ThreadLocalMap` certainly needs to implement its own `hash` algorithm to address the issue of collision in the hash table array.

```java
int i = key.threadLocalHashCode & (len - 1);
```

The `hash` algorithm in `ThreadLocalMap` is quite simple; here `i` is the index position of the current key in the hash table.

The most crucial aspect is the calculation of the `threadLocalHashCode` value. The `ThreadLocal` contains a property `HASH_INCREMENT = 0x61c88647`.

```java
public class ThreadLocal<T> {
    private final int threadLocalHashCode = nextHashCode();

    private static AtomicInteger nextHashCode = new AtomicInteger();

    private static final int HASH_INCREMENT = 0x61c88647;

    private static int nextHashCode() {
        return nextHashCode.getAndAdd(HASH_INCREMENT);
    }

    static class ThreadLocalMap {
        ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
            table = new Entry[INITIAL_CAPACITY];
            int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);

            table[i] = new Entry(firstKey, firstValue);
            size = 1;
            setThreshold(INITIAL_CAPACITY);
        }
    }
}
```

Every time a `ThreadLocal` object is created, the value of `ThreadLocal.nextHashCode` increases by `0x61c88647`.

This value is quite special; it is a **Fibonacci number**, also known as the **golden ratio**. The `hash` increment of this number ensures that the `hash` is **evenly distributed**.

We can try it out:

![](./images/thread-local/8.png)

The generated hash codes are distributed very evenly. We won't go into the specifics of the **Fibonacci** algorithm here; interested readers can look up related materials themselves.

### Hash Collisions in `ThreadLocalMap`

> **Note:** In all example images below, **green blocks** represent **normal data**, **gray blocks** represent `Entry` with `key` value of `null`, having **been garbage collected**. **White blocks** indicate `Entry` is `null`.

Although `ThreadLocalMap` uses the **golden ratio** as a factor for hash computation, greatly reducing the probability of hash collisions, conflicts may still occur.

`HashMap` resolves conflicts by constructing a **linked list** structure in the array. Conflicting data is appended to the list, and if the list length exceeds a certain number, it is transformed into a **red-black tree**.

However, `ThreadLocalMap` does not have a linked list structure, so we cannot use the HashMap method for conflict resolution.

![](./images/thread-local/7.png)

As shown in the diagram above, when we insert data with `value=27`, it is calculated to fall into slot 4, but slot 4 already has `Entry` data.

At this point, a linear search will occur, continuing until an `Entry` with a value of `null` is found to stop searching, placing the current element in that slot. Of course, there are other scenarios during the iteration, such as encountering an entry where `key` is equal or an `Entry` where `key` is null, etc., all of which will have different treatments that will be explained later.

There is also data in `Entry` whose `key` is `null` (**the gray block data of Entry=2**). Since the `key` is a **weak reference** type, such data may exist. During the `set` process, if an expired `Entry` is encountered, a round of **probe cleanup** will actually occur, and the specific operation will be discussed later.

### `ThreadLocalMap.set()` Detailed Explanation

#### `ThreadLocalMap.set()` Principle Diagram

After understanding the `hash algorithm` of `ThreadLocal`, let's look at how `set` is implemented.

Setting data in `ThreadLocalMap` (either **adding** or **updating** data) can be divided into several scenarios, which we will explain with illustrations.

**Scenario One:** The slot corresponding to the key calculated by hash is empty:

![](./images/thread-local/9.png)

Here, the data can be placed directly into that slot.

**Scenario Two:** The slot data is not empty, and the key value matches the current `ThreadLocal` obtained through `hash` calculation:

![](./images/thread-local/10.png)

Here, the data in the slot is updated directly.

**Scenario Three:** The slot data is not empty, and during the subsequent traversal, before finding an `Entry` with a value of `null`, an expired `Entry` is not encountered:

![](./images/thread-local/11.png)

In this case, iterate through the hash array and perform a linear search. If an `Entry` with a value of `null` is found in the slot, place the data there or update directly upon encountering an **equal key** during the search.

**Scenario Four:** The slot data is not empty, and during the subsequent traversal, an expired `Entry` is encountered before finding an `Entry` with a value of `null`. For example, if an expired data `Entry` is at `index=7`:

![](./images/thread-local/12.png)

The slot at array index 7 has `Entry` data with a `key` of `null`, indicating that the key has already been garbage collected. At this point, the `replaceStaleEntry()` method will be executed, which is the logic for **replacing expired data**, starting the search at **index=7** for probing cleanup.

To initialize the scanning start position for probing cleanup for expired data: `slotToExpunge = staleSlot = 7`

Starting from the current `staleSlot`, iterate forward to find other expired data, and update the starting scan index `slotToExpunge`. The `for` loop will iterate until it encounters an `Entry` with a value of `null`.

If expired data is found, continue iterating until discovering an `Entry` with a value of `null` to stop the iteration, as shown in the diagram below; **slotToExpunge gets updated to 0**:

![](./images/thread-local/13.png)

Iterating forward from the current node (`index=7`), check for expired `Entry` data. If it is found, update the value of `slotToExpunge`. Stop probing when encountering `null`. In this example, `slotToExpunge` was updated to 0.

The mentioned forward iteration operation's purpose is to update the value of `slotToExpunge`, which will serve later to determine whether there are any expired elements before the current stale slot.

Next, begin iterating backward from the `staleSlot` position (`index=7`); **if an `Entry` with the same key value is found:**

![](./images/thread-local/14.png)

Search for `Entry` elements with an equal key value starting from the current node `staleSlot`. If found, update the value of `Entry` and swap with the `staleSlot` element (the `staleSlot` location is the expired element); update the `Entry` data and initiate cleanup for expired `Entry`, as shown in the diagram:

![](https://oss.javaguide.cn/java-guide-blog/view.png) During traversal, if no `Entry` with the same key value is found:

![](./images/thread-local/15.png)

Continue searching for an `Entry` with the same key value from the current `staleSlot` until reaching a slot with `Entry` set as `null` to stop searching. As seen in the above diagram, there are no `Entry` values with the same key.

Create a new `Entry` to replace the position of `table[stableSlot]`:

![](./images/thread-local/16.png)

Once the replacement is complete, also run the expired elements cleanup operation, primarily two methods: `expungeStaleEntry()` and `cleanSomeSlots()`. We will cover the specifics later, so please keep reading.

#### Source Code for `ThreadLocalMap.set()`

Having illustrated the principles of `set()` with diagrams, it's already quite clear. Let’s delve into the source code:

`java.lang.ThreadLocal.ThreadLocalMap.set()`:

```java
private void set(ThreadLocal<?> key, Object value) {
    Entry[] tab = table;
    int len = tab.length;
    int i = key.threadLocalHashCode & (len - 1);

    for (Entry e = tab[i];
         e != null;
         e = tab[i = nextIndex(i, len)]) {
        ThreadLocal<?> k = e.get();

        if (k == key) {
            e.value = value;
            return;
        }

        if (k == null) {
            replaceStaleEntry(key, value, i);
            return;
        }
    }

    tab[i] = new Entry(key, value);
    int sz = ++size;
    if (!cleanSomeSlots(i, sz) && sz >= threshold)
        rehash();
}
```

This method calculates the corresponding position in the hash table based on the `key`, then iteratively looks for an available bucket starting from the current key's position.

```java
Entry[] tab = table;
int len = tab.length;
int i = key.threadLocalHashCode & (len - 1);
```

When is a bucket usable?

1. If `k = key`, it indicates this is a replacement operation.
1. If a bucket containing an expired `Entry` is encountered, replace it with new logic.
1. When encountering a bucket whose `Entry` is `null`.

Next in the loop:

1. If the `Entry` data at the current bucket corresponding to `key` is `null`, this indicates no data conflict. Break out of the loop and set data directly into this bucket.
1. If the `Entry` data at the current bucket corresponding to `key` is not empty:\
   2.1 If `k = key`, it signifies the current `set` operation is for replacement. Execute the replacement logic and return.\
   2.2 If `key = null`, signifying that the current bucket’s `Entry` contains expired data, invoke `replaceStaleEntry()` (the core method) before returning.
1. If the loop concludes without finding the `key`, create a `new Entry` object in the bucket with `Entry` set to `null`.
1. After that, invoke `cleanSomeSlots()` to carry out heuristic cleanup of expired keys in the hash table. If no data was cleaned, and size exceeds the threshold (two-thirds of array length), proceed to `rehash()`.

Next, closely review the `replaceStaleEntry()` method, which provides the functionality to replace expired data. You can relate it to the principle diagram in **Scenario Four** provided earlier:

`java.lang.ThreadLocal.ThreadLocalMap.replaceStaleEntry()`:

```java
private void replaceStaleEntry(ThreadLocal<?> key, Object value,
                                       int staleSlot) {
    Entry[] tab = table;
    int len = tab.length;
    Entry e;

    int slotToExpunge = staleSlot;
    for (int i = prevIndex(staleSlot, len);
         (e = tab[i]) != null;
         i = prevIndex(i, len))

        if (e.get() == null)
            slotToExpunge = i;

    for (int i = nextIndex(staleSlot, len);
         (e = tab[i]) != null;
         i = nextIndex(i, len)) {

        ThreadLocal<?> k = e.get();

        if (k == key) {
            e.value = value;

            tab[i] = tab[staleSlot];
            tab[staleSlot] = e;

            if (slotToExpunge == staleSlot)
                slotToExpunge = i;
            cleanSomeSlots(expungeStaleEntry(slotToExpunge), len);
            return;
        }

        if (k == null && slotToExpunge == staleSlot)
            slotToExpunge = i;
    }

    tab[staleSlot].value = null;
    tab[staleSlot] = new Entry(key, value);

    if (slotToExpunge != staleSlot)
        cleanSomeSlots(expungeStaleEntry(slotToExpunge), len);
}
```

The `slotToExpunge` indicates the start index for probing cleanup of expired data, initially starting at the `staleSlot`. The search continues from `staleSlot`, iterating forward to find non-expired data to potentially update `slotToExpunge`. The `for` loop iterates until encountering an `Entry` that is `null`.

```java
for (int i = prevIndex(staleSlot, len);
     (e = tab[i]) != null;
     i = prevIndex(i, len)){

    if (e.get() == null){
        slotToExpunge = i;
    }
}
```

Next, the search from `staleSlot` indices forward occurs, and when encountering a key equal to the passed `key`, the element is replaced and swap positions.

```java
if (k == key) {
    e.value = value;
    tab[i] = tab[staleSlot];
    tab[staleSlot] = e;

    if (slotToExpunge == staleSlot)
        slotToExpunge = i;

    cleanSomeSlots(expungeStaleEntry(slotToExpunge), len);
    return;
}
```

If `k != key`, continue searching forward in the probing process. If `k == null`, then the currently checked `Entry` is expired, indicating a potential update for `slotToExpunge`.

```java
if (k == null && slotToExpunge == staleSlot)
    slotToExpunge = i;
```

When no matching `k == key` is found, but an `Entry` becomes `null`, the addition operation occurs where a new `Entry` is created for the `table[staleSlot]`.

```java
tab[staleSlot].value = null;
tab[staleSlot] = new Entry(key, value);
```

Finally, if `slotToExpunge` differs from `staleSlot`, cleanup operations begin anew.

```java
if (slotToExpunge != staleSlot)
    cleanSomeSlots(expungeStaleEntry(slotToExpunge), len);
```

### Probing Cleanup Process for Expired Keys in `ThreadLocalMap`

Above, we mentioned the two cleanup methods for expired keys in `ThreadLocalMap`: **probing cleanup** and **heuristic cleanup**.

We first discuss probing cleanup, specifically in the `expungeStaleEntry` method, which iterates through the hash array, probing and cleaning expired data, setting the value of the `Entry` to null. If a non-expired bucket is encountered, the values will be relocated.

![](./images/thread-local/18.png)

In the diagram, `set(27)` is calculated to be in `index=4`; since it already has data, it will continue iterating forward, leading to data being moved to `index=7`. After a while, the `Entry` data at `index=5` key becomes `null`.

![](./images/thread-local/19.png)

When additional `set` operations to map occur, this will trigger **probing cleanup**.

After this operation, expired data will be cleaned up, and non-expired data will be recalled after capturing the appropriate hash key slot position more properly.

Next, let’s see the specific flow of `expungeStaleEntry()`. We adopt the principle-then-source-code approach to explain step by step.

![](./images/thread-local/20.png)

Assume calling `expungeStaleEntry(3)` leads to the following observations, showcasing the data situation in `ThreadLocalMap`:

![](./images/thread-local/21.png)

The first step clears the current `staleSlot`, changing the `index=3` data to `null`. The probing continues forward:

![](./images/thread-local/22.png)

After completing Step 2, the `index=4` data is moved到 the `index=3` slot.

As the iteration proceeds to verify whether normal data encounters an offset at an appropriate position.

![](./images/thread-local/23.png)

The traversal continues; upon encountering an empty slot, it terminates the probing cleanup while confirming that this round of cleaning is complete. This completes a round of probing cleanup work, and now we can view the implementation in detail:

```java
private int expungeStaleEntry(int staleSlot) {
    Entry[] tab = table;
    int len = tab.length;

    tab[staleSlot].value = null;
    tab[staleSlot] = null;
    size--;

    Entry e;
    int i;
    for (i = nextIndex(staleSlot, len);
         (e = tab[i]) != null;
         i = nextIndex(i, len)) {
        ThreadLocal<?> k = e.get();
        if (k == null) {
            e.value = null;
            tab[i] = null;
            size--;
        } else {
            int h = k.threadLocalHashCode & (len - 1);
            if (h != i) {
                tab[i] = null;

                while (tab[h] != null)
                    h = nextIndex(h, len);
                tab[h] = e;
            }
        }
    }
    return i;
}
```

Using `staleSlot=3` as an example, the process starts by clearing the data at `tab[staleSlot]`, and then the value of `size` decreases.

Next to confirm if the value has expired, indicating a mark for cleaning.

```java
ThreadLocal<?> k = e.get();

if (k == null) {
    e.value = null;
    tab[i] = null;
    size--;
}
```

If the key is still valid, its corresponding (`Entry`s)\` index will be calculated allowing the relevant data to be relocated in the table.

```java
int h = k.threadLocalHashCode & (len - 1);
if (h != i) {
    ...
}
```

The only way for correct data to avoid `hash` collisions will be to ensure it has been relocated at the most nearby possible point.

### Expansion Mechanism of `ThreadLocalMap`

At the conclusion of `ThreadLocalMap.set()`, if after running the heuristic cleanup, no data has been cleaned and the current number of `Entry`s exceeds a threshold of `(len*2/3)`, the method begins the `rehash()` logic:

```java
if (!cleanSomeSlots(i, sz) && sz >= threshold)
    rehash();
```

To comprehend, let's investigate how `rehash()` is specifically implemented:

```java
private void rehash() {
    expungeStaleEntries();

    if (size >= threshold - threshold / 4)
        resize();
}

private void expungeStaleEntries() {
    Entry[] tab = table;
    int len = tab.length;
    for (int j = 0; j < len; j++) {
        Entry e = tab[j];
        if (e != null && e.get() == null)
            expungeStaleEntry(j);
    }
}
```

The first step entails performing a probing cleanup for stale entries, iterating through `table` forwards. Following the cleanup, if `size >= threshold - threshold / 4` (which is equivalent to `size >= threshold * 3/4`), this indicates a need to expand.

Be sure to make the distinction when discussing `ThreadLocalMap`'s expansion mechanism; note both steps.

![](./images/thread-local/24.png)

Next, looking at the specific implementation of `resize()` for clarity, we'll use an example with `oldTab.len=8`:

![](./images/thread-local/25.png)

With the new `tab` size being `oldLen * 2`, an iteration over the former has the hash positioning recalculated, effectively moving all existing data into the newly generated `tab`. The subsequent threshold for re-expansion will be recalculated accordingly.

```java
private void resize() {
    Entry[] oldTab = table;
    int oldLen = oldTab.length;
    int newLen = oldLen * 2;
    Entry[] newTab = new Entry[newLen];
    int count = 0;

    for (int j = 0; j < oldLen; ++j) {
        Entry e = oldTab[j];
        if (e != null) {
            ThreadLocal<?> k = e.get();
            if (k == null) {
                e.value = null;
            } else {
                int h = k.threadLocalHashCode & (newLen - 1);
                while (newTab[h] != null)
                    h = nextIndex(h, newLen);
                newTab[h] = e;
                count++;
            }
        }
    }

    setThreshold(newLen);
    size = count;
    table = newTab;
}
```

### Detailed Explanation of `ThreadLocalMap.get()`

Now that we’ve completed examining the `set()` method source code, which consists of setting and cleaning data operations as well, the next step is to look into the principles behind the `get()` operation.

#### Diagram for `ThreadLocalMap.get()`

**Scenario One:** When the key value is lookup based on hash in the slot position, if the `Entry.key` matches the key, return immediately:

![](./images/thread-local/26.png)

**Scenario Two:** If the `Entry.key` differs from the intended key value:

![](./images/thread-local/27.png)

Assuming we use `get(ThreadLocal1)`, if calculation via hash indicates the correct slot is index 4 but that slot contains data where the key property doesn’t match `ThreadLocal1`, we will need to continue searching.

Upon reaching data in `index=5`, if `Entry.key=null`, this activates a probe cleanup, invoking `expungeStaleEntry()` method while iterating stops at encountering any remaining `Entry` as `null`.

After cleaning, we can continue considering the next slot; if we maintain searching through for matching key values, we will eventually arrive back to entry with a matching `key`.

#### Source Code for `ThreadLocalMap.get()`

`java.lang.ThreadLocal.ThreadLocalMap.getEntry()`:

```java
private Entry getEntry(ThreadLocal<?> key) {
    int i = key.threadLocalHashCode & (table.length - 1);
    Entry e = table[i];
    if (e != null && e.get() == key)
        return e;
    else
        return getEntryAfterMiss(key, i, e);
}

private Entry getEntryAfterMiss(ThreadLocal<?> key, int i, Entry e) {
    Entry[] tab = table;
    int len = tab.length;

    while (e != null) {
        ThreadLocal<?> k = e.get();
        if (k == key)
            return e;
        if (k == null)
            expungeStaleEntry(i);
        else
            i = nextIndex(i, len);
        e = tab[i];
    }
    return null;
}
```

### Heuristic Cleanup Process for Expired Keys in `ThreadLocalMap`

We’ve previously mentioned two cleanup methods for expired keys in `ThreadLocalMap`: **probe cleanup (expungeStaleEntry())** and **heuristic cleanup (cleanSomeSlots())**.

Probationary cleanup is specific about the current `Entry` being cleaned in succession until the `null` values finally stop.

Heuristic cleanup, however, has been defined as: **Heuristically scan some cells looking for stale entries**.

![](./images/thread-local/29.png)

The specific code is as follows:

```java
private boolean cleanSomeSlots(int i, int n) {
    boolean removed = false;
    Entry[] tab = table;
    int len = tab.length;
    do {
        i = nextIndex(i, len);
        Entry e = tab[i];
        if (e != null && e.get() == null) {
            n = len;
            removed = true;
            i = expungeStaleEntry(i);
        }
    } while ((n >>>= 1) != 0);
    return removed;
}
```

### `InheritableThreadLocal`

When we use `ThreadLocal`, in asynchronous scenarios, child threads cannot share the thread-local copies of data created in parent threads.

To address this problem, JDK provides the `InheritableThreadLocal` class. Let’s see an example:

```java
public class InheritableThreadLocalDemo {
    public static void main(String[] args) {
        ThreadLocal<String> threadLocal = new ThreadLocal<>();
        InheritableThreadLocal<String> inheritableThreadLocal = new InheritableThreadLocal<>();
        threadLocal.set("Parent data: threadLocal");
        inheritableThreadLocal.set("Parent data: inheritableThreadLocal");

        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("Child thread gets parent ThreadLocal data: " + threadLocal.get());
                System.out.println("Child thread gets parent inheritableThreadLocal data: " + inheritableThreadLocal.get());
            }
        }).start();
    }
}
```

Output:

```java
Child thread gets parent ThreadLocal data: null
Child thread gets parent inheritableThreadLocal data: Parent data: inheritableThreadLocal
```

The implementation principle is that child threads are initialized through the parent thread by calling the `new Thread()` method. The `Thread#init` method within the `Thread` constructor is invoked at this point, during which parent thread data is copied over to the child thread:

```java
private void init(ThreadGroup g, Runnable target, String name,
                      long stackSize, AccessControlContext acc,
                      boolean inheritThreadLocals) {
    if (name == null) {
        throw new NullPointerException("name cannot be null");
    }

    if (inheritThreadLocals && parent.inheritableThreadLocals != null)
        this.inheritableThreadLocals =
            ThreadLocal.createInheritedMap(parent.inheritableThreadLocals);
    this.stackSize = stackSize;
    tid = nextThreadID();
}
```

However, `InheritableThreadLocal` still has drawbacks, as asynchronous processing typically utilizes thread pools; `InheritableThreadLocal` is set during the `init()` method under `new Thread`, meaning potential concerns arise regarding thread reuse.

Fortunately, when issues arise, solutions surface. Alibaba has open-sourced a `TransmittableThreadLocal` component, which resolves this problem. Those interested may consult additional resources.

### Practical Uses of `ThreadLocal` in Projects

#### Usage Scenarios for `ThreadLocal`

In our project, logging is done using `ELK + Logstash`, with `Kibana` employed for display and retrieval.

Currently, services deploy distributed systems providing services externally; inter-project call relationships can be linked via `traceId`. However, how should `traceId` be transferred between different projects?

Here we employ `org.slf4j.MDC` to achieve this, which is implemented internally via `ThreadLocal`. The specific execution is as follows:

When the frontend sends a request to **Service A**, **Service A** generates a `traceId` string akin to `UUID`, placing it into the current thread's `ThreadLocal`. In calling **Service B**, this `traceId` is inserted into the request's headers; upon **Service B** receiving the request, it first examines whether the request’s headers contain `traceId`, and if present, it writes it into its own thread’s `ThreadLocal`.

![](./images/thread-local/30.png)

In the diagram, `requestId` represents our `traceId` associated with different system chains, enabling mutual calls. This also permits additional scenarios:

![](./images/thread-local/31.png)

For these scenarios, we can devise appropriate solutions, as outlined below.

#### Feign Remote Call Solution

**Service Sending Request:**

```java
@Component
@Slf4j
public class FeignInvokeInterceptor implements RequestInterceptor {

    @Override
    public void apply(RequestTemplate template) {
        String requestId = MDC.get("requestId");
        if (StringUtils.isNotBlank(requestId)) {
            template.header("requestId", requestId);
        }
    }
}
```

**Service Receiving Request:**

```java
@Slf4j
@Component
public class LogInterceptor extends HandlerInterceptorAdapter {

    @Override
    public void afterCompletion(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2, Exception arg3) {
        MDC.remove("requestId");
    }

    @Override
    public void postHandle(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2, ModelAndView arg3) {
    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String requestId = request.getHeader(BaseConstant.REQUEST_ID_KEY);
        if (StringUtils.isBlank(requestId)) {
            requestId = UUID.randomUUID().toString().replace("-", "");
        }
        MDC.put("requestId", requestId);
        return true;
    }
}
```

#### Passing requestId in Asynchronous Thread Pool Calls

Since `MDC` is implemented based on `ThreadLocal`, during asynchronous execution, child threads cannot access data stored in the parent thread's `ThreadLocal`. Therefore, we can add a customized thread pool executor and modify the `run()` method:

```java
public class MyThreadPoolTaskExecutor extends ThreadPoolTaskExecutor {

    @Override
    public void execute(Runnable runnable) {
        Map<String, String> context = MDC.getCopyOfContextMap();
        super.execute(() -> run(runnable, context));
    }

    @Override
    private void run(Runnable runnable, Map<String, String> context) {
        if (context != null) {
            MDC.setContextMap(context);
        }
        try {
            runnable.run();
        } finally {
            MDC.remove();
        }
    }
}
```

#### Using MQ to Send Messages to Third-Party Systems

A custom attribute named `requestId` is added to the message body in MQ. Upon consumer retrieval of the message, they can decode and utilize `requestId` at will.
