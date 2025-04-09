I don't...
type: ThreadLocal
Category:
Tag:

- Java.
  I don't...

> This is a non-romantic contribution from a flower, original address: [https://juejin.cn/post/6844904155757040519](https://juejin.cn/post/6844490415156700519).

Foreword

(./images/thread-local/1.png)

**The full text is 10,000+ words, 31 drawings. The article also took a lot of time and effort to complete the original, which was hard to create.**

In the case of `ThreadLocal`, the first reaction may be simple, with a copy of the variable of the thread, separated from each. There are a few questions you can think about:

- `ThreadLocal` key is **weak reference**, so when `ThreadLocal.get()`, **GC**, **null**?
- **Data structure of `ThreadLocal` in `ThreadLocalMap`?**
- `ThreadLocalMap` **Hash algorithm**?
- How can the `ThreadLocalMap` conflict **be resolved?**
- `ThreadLocalMap` **amplification mechanism**?
- Clean-up mechanism in `ThreadLocalMap` **for expired key?** **Discovery clean-up** and **inspired clean-up** process?
- `ThreadLocalMap.set()` Method of realization?
- `ThreadLocalMap.get()` Method of realization?
- Use of `ThreadLocal` in the project? The pit?
- ...

Are some of the above questions well known to you? This paper will use the graphics approach to the `ThreadLocal` **points and drops of `ThreadLocal`.**

## Directory

**Specify:** This source code is based on `JDK 1.8`

### `ThreadLocal` code presentation

Let us first look at the example of the use of `ThreadLocal`:

```Java
I can't believe it.
= Lists.newArrayList();

Public service financial ThreadLocal<ThreadLocalTest> holder = ThreadLocal.withInitial(ThreadLocalTest::new);

Public status void
Holder.get().messages.add(message);
♪ I'm sorry ♪

Public status List
List<String> messages = holder.get().messages;
Holder.remove();

System.out.println("size: " + holder.get().messages.size());
I'm sorry.
♪ I'm sorry ♪

Public status void Main
ThreadLocalTest.add("A flower is not romantic");
System.out.println(holder.get().messages);
ThreadLocalTest.clear();
♪ I'm sorry ♪
♪ I'm sorry ♪
```

Print results:

```Java
[A flower is not romantic]
size: 0
```

The `ThreadLocal` object provides a local range variable, each of which `Thread` has a copy of its own **variable** and multiple threads do not interfere.

### `ThreadLocal` data structure

![./images/thread-local/2.png]

The category `Thread` has an example variable of `ThreadLocalMap`, i.e. `ThreadLocalMap` per thread.

`ThreadLocalMap` has its own independent realization and can simply be considered `key` as `ThreadLocal`, `value` as the value inserted in the code (in fact `key` is not `ThreadLocal` per se, but it is a weak reference).

Each thread is stored in its own `ThreadLocalMap`, and is also quoted as `ThreadLocal`, where the corresponding `key` is found in its `map`, thus **wiring is isolated.**

`ThreadLocalMap` is a bit similar to `HashMap`, except that `HashMap` was achieved by **Numerical + Chain**, while `ThreadLocalMap` does not have **Chain** Structure.

We would also like to note `Entry`, whose `key` is `ThreadLocal<?> k`, inherited from `WeakReference`, which is the weak type of reference we often refer to.

### After GC is key full?

In response to the question at the beginning, the `key` of `ThreadLocal` is a weak quote, so when `ThreadLocal.get()`, after the `GC` happens, `key` is `null`?

In order to understand this, we need to understand the four types of references of `Java`:

- **Strong Reference**: We are often new to the type of reference, and as long as it exists, the garbage collector can never reclaim it, even when memory is insufficient.
- **Soft Reference**: Objects with SoftReference are known as soft references, which are recycled when the object to which the soft reference refers is expected to spill over.
- **Weak Reference**: The object using the Weak Reference is known as the weak reference, and if garbage collection occurs, the object will be reclaimed if only the weak reference pointers exist.
- **Phantom Reference**: Phantom reference is the weakest reference, defined in Java by the use of PhantomReference. The only function of a phantom reference is to receive a queuing notification of an object dying.

Then we look at the code, and we look
