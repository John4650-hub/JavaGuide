I don't...
Title: Java with summary of common interview (C)
Category:
Tag:
- Java
Head:
- Meta
- Name: keywords
Content: Multiple threads, deadlocks, synchronized, ReentrantLock, volatile, ThreadLocal, thread pool, CAS, AQS
- Meta
- Name: description
Content: Java with a summary of common knowledge points and interviews (with detailed answers).
I don't...

@include: @article-header.snippet.md--

# ⭐️ JMM (Java Memory Model)

The JMM (Java Memory Model) is more proficient and important, so I singled out an article to summarize the knowledge points and problems associated with JMM: [JMM (Java Memory Model) detailed](./jmm.md).

# volatile keyword

How do you guarantee the visibility of the variable?

In Java, the `volatile` keyword ensures the visibility of the variable. If we declare the variable as `volatile`, then the JVM is instructed that this variable is shared and unstable, and is read in the main memory every time it is used.

![Image 1](https://oss.javaguide.cn/github/java/java/concurrent/jmm.png)

![Image 2](https://oss.javaguide.cn/github/javaguide/java/concurrent/jmm2.png)

The `volatile` keyword is not really unique to Java, but also exists in C, and its original meaning is to disable CPU caches. If we use a variable with `volatile` modifications, then this indicates that the compiler treats it as a shared and unstable variable, which is read in the main memory every time it is used.

The `volatile` keyword ensures the visibility of the data, but not the atomicity of the data. `synchronized` guarantees both.

How can you forbid the reordering of commands?

**In Java, the `volatile` keyword, in addition to ensuring the visibility of variables, has an important role in preventing the reordering of JVM directives.** If we declare the variable as `volatile`, the reordering of the command will be prohibited by inserting a specific **memory barrier** when this variable is read and written.

In Java, the `Unsafe` class provides three memory barrier-related methods that shield differences at the bottom of the operating system:

```Java
(a) public native void loadFence();
(b) public native void storeFence();
(c) public native void fullFence();
```

In theory, you can also achieve the same effect as the `volatile` prohibition of reordering in these three ways, but it would be more troublesome.

I will now give an example of the effect of the `volatile` keyword prohibiting the reordering of instructions in a common interview.

Interviewers in interviews often say, "Do you understand the singleton pattern? Write it for me! Explain to me the principle of double-checked locking for a singleton."

**Double-checked locking achieves a singleton object (linear security)**:

```Java
public class Singleton {
    private static volatile Singleton uniqueInstance;

    private Singleton() {}

    public static Singleton getUniqueInstance() {
        // First judge whether the object has been instantiated or not before entering the locking code
        if (uniqueInstance == null) {
            // Class object lock
            synchronized (Singleton.class) {
                if (uniqueInstance == null) {
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }
}
```

`uniqueInstance` is also necessary to introduce the `volatile` keyword. The code `uniqueInstance = new Singleton();` is actually implemented in three steps:

1. Allocation of memory space for `uniqueInstance`
2. Initialization of `uniqueInstance`
3. Point `uniqueInstance` to assigned memory addresses

However, because the JVM has command reordering characteristics, the order of execution is likely to be 1->3->2. The reordering of the command would not cause problems in a single-threaded environment, but in a multi-threaded environment, it would lead to an example of a thread that had not yet been initialized. For example, thread T1 executed 1 and 3 at a time when T2 called `getUniqueInstance()` and found that `uniqueInstance` was not null and returned `uniqueInstance`, but at a time when `uniqueInstance` had not been initialized.

# Will the volatile guarantee atomicity?

**The `volatile` keyword ensures the visibility of the variable, but does not guarantee that the operation of the variable is atomic.**

We can prove this by the following code:

```Java
/**
 * The JavaGuide interview manual, originally created by the individual, is available free of charge in response to the interview raid
 */
public class VolatileAtomicityDemo {
    private static volatile int inc = 0;

    public static void main(String[] args) throws InterruptedException {
        ExecutorService threadPool = Executors.newFixedThreadPool(5);
        VolatileAtomicityDemo demo = new VolatileAtomicityDemo();
        for (int i = 0; i <