---
title: ArrayBlockingQueue Source Code Analysis
category: Java
tag:
  - Java Collections
---

## Introduction to Blocking Queues

### History of Blocking Queues

The history of Java's blocking queues can be traced back to JDK 1.5, when the Java platform introduced `java.util.concurrent`, commonly referred to as the JUC package, which includes various concurrency control tools, concurrent containers, atomic classes, and more. Naturally, this also includes the blocking queues discussed in this article.

To address the issue of data sharing among multiple threads in high-concurrency scenarios, JDK 1.5 introduced `ArrayBlockingQueue` and `LinkedBlockingQueue`, which are concurrent containers implementing the producer-consumer pattern. Among them, `ArrayBlockingQueue` is a bounded queue, meaning that once the number of added elements reaches its limit, any further additions will be blocked or throw an exception. In contrast, `LinkedBlockingQueue` is a queue constructed from a linked list, and due to the nature of linked lists, `LinkedBlockingQueue` does not impose as many constraints on adding elements as `ArrayBlockingQueue`, allowing it to optionally be bounded (note that "unbounded" here does not mean it can accept an arbitrary number of elements, but rather that the default size of the queue is `Integer.MAX_VALUE`, which is nearly infinite).

As Java has continued to evolve, subsequent versions of JDK have made numerous updates and improvements to blocking queues:

1. JDK 1.6: Introduced `SynchronousQueue`, a blocking queue that does not store elements.
2. JDK 1.7: Introduced `TransferQueue`, a blocking queue that supports more operations.
3. JDK 1.8: Introduced `DelayQueue`, a blocking queue that supports delayed retrieval of elements.

### The Concept of Blocking Queues

A blocking queue is a typical producer-consumer model that can achieve the following:

1. When the blocking queue is empty, all consumer threads will be blocked, waiting for the queue to become non-empty.
2. When the producer fills the queue with data, the queue will notify consumers that it is non-empty, allowing them to consume.
3. When the blocking queue is full due to slow consumption or fast production, the producer will be blocked, waiting until the queue is not full to continue adding elements.
4. After a consumer consumes an element from the queue, the queue will notify the producer that it is not full, allowing the producer to continue adding data.

In summary, the blocking queue facilitates interaction between producers and consumers based on the conditions of being non-empty and non-full. Although the implementation of these interaction processes and the waiting-notification mechanism is quite complex, thanks to Doug Lea's work, the details of blocking queues are abstracted away, and we only need to call APIs like `put`, `take`, `offer`, and `poll` to achieve production and consumption between multiple threads.

This has led to the widespread use of blocking queues in multithreaded development, with the most common example being our thread pools. From the source code, we can see that when core threads cannot process tasks in time, these tasks are thrown into the `workQueue`.

```java
public ThreadPoolExecutor(int corePoolSize,
                            int maximumPoolSize,
                            long keepAliveTime,
                            TimeUnit unit,
                            BlockingQueue<Runnable> workQueue,
                            ThreadFactory threadFactory,
                            RejectedExecutionHandler handler) {// ...}
```

## Common Methods and Testing of ArrayBlockingQueue

After briefly understanding the history of blocking queues, we will focus on the concurrent container that this article aims to introduce—`ArrayBlockingQueue`. To gain a deeper understanding of `ArrayBlockingQueue`, let's explore its usage through the following examples.

First, let's look at the first example, where we will use two threads to simulate a producer and a consumer. The producer will produce 10 elements using the `put` method for the consumer to consume. When the number of elements in the queue reaches our set limit of 5, the `put` method will block.
Similarly, the consumer will consume elements using the `take` method, and when the queue is empty, the `take` method will block the consumer thread. To ensure that the consumer can exit promptly after consuming 10 elements, I used a countdown latch to control the consumer's termination, while the producer will only produce 10 elements. After the consumer has consumed all 10 elements, it will press the countdown latch, causing all threads to stop.

```java
public class ProducerConsumerExample {

    public static void main(String[] args) throws InterruptedException {

        // Create an ArrayBlockingQueue with a size of 5
        ArrayBlockingQueue<Integer> queue = new ArrayBlockingQueue<>(5);

        // Create producer thread
        Thread producer = new Thread(() -> {
            try {
                for (int i = 1; i <= 10; i++) {
                    // Add elements to the queue; if the queue