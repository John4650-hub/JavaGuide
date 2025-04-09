I don't...
type: Array BlockingQueue source analysis
Category:
Tag:
- Java.
I don't...

# Block Queue Profile

The history of blocking the queue

The history of Java blocking queues can be traced back to JDK version 1.5, when the Java platform added `java.util.concurrent`, what we often call the JUC package, which contains a variety of co-dispatch process control tools, co-dispatch containers, atoms, etc. This naturally also includes the blocking queues that we discussed in this article.

In order to address the problem of data-sharing between multiple threads under a high-mixed scenario, the JDK 1.5 version includes `ArrayBlockingQueue` and `LinkedBlockingQueue`, which are co-containers with a producer-consumer model. Of these, `ArrayBlockingQueue` is a line, i.e., once the added element reaches the upper limit, re-adding is blocked or abnormally dropped. `LinkedBlockingQueue`, however, consists of a queue of chains; precisely because of the characteristics of the chain table, `LinkedBlockingQueue` would not be as restrained on additions as much as `ArrayBlockingQueue`, so `LinkedBlockingQueue` setup is optional or not (noting that the lack of boundaries here does not mean that any number of elements can be added, but rather that the size of the queue is by default `Integer.MAX_VALUE`, which is close to unlimited).

As Java continues to develop, several subsequent versions of JDK have updated and refined the blocking queue:

1. JDK Version 1.6: Add `SynchronousQueue`, a block queue without storage of elements.
2. JDK Version 1.7: Add `TransferQueue`, a block queue to support more operations.
3. JDK Version 1.8: Add `DelayQueue`, a block queue that supports delayed acquisition of elements.

# To Block the Queue

Blocking queues are typical producer-consumer models that can do the following:

1. When the data for blocking the queue is empty, all consumer lines are blocked until the queue is empty.
2. When producers fill in the queue, the queue informs consumers that the queue is not empty, and consumers can come in and consume it.
3. When blocking queues are unable to accommodate new elements when they are filled because consumer consumption is too slow or producers are too fast to store them, producers are blocked until they can continue to store the elements when the queue is not full. When consumers consume an element from the queue, the queue informs producers that the queue is not full and that producers can continue to fill the data.

Summarizing: The blocking of queues is said to result in interaction between producers and consumers based on non-empty and non-full conditions, although these interaction processes and mechanisms for waiting for notification have become very complex, and it is good that the details of blocking queues have been shielded under Doug Lea's machetes, and we need only call API `put`, `take`, `offer`, `poll`, and others to achieve production and consumption between multiple lines.

This has also led to the extensive use of blockers in multi-thread development, and the most common example is our work queue, which can be seen in the source code as being thrown into `workQueue` when core threads cannot be handled in a timely manner.

```
Java
I'm sorry, but I don't know what I'm talking about.
I'm sorry.
I don't know.
I'm sorry.
Blocking Queue, work Queue,
ThreadFactory
ProjectedExplicitHandler Handler)
```

# Array Blocking Queue Common Methods and Tests

After a brief review of the history of blocking the queue, we began to focus on the simultaneous container `ArrayBlockingQueue`, which is to be presented in this article. In order to follow up on the `ArrayBlockingQueue`, we may wish to learn about the use of `ArrayBlockingQueue`, based on the following examples.

In the first example, we are here to simulate producers and consumers by two threads, and producers are able to produce 10 elements for consumers after production using the `put` method, which is blocked when the `put` method reaches the ceiling we set. The same consumer also consumes elements through the `take` method, which, when the queue is listed as empty, will block the consumer's circuit. Here's to make sure consumers can quit after 10 elements are consumed. And it's a countdown to control the end of the consumer, where producers produce only 10 elements. When consumers complete consumption of 10 elements, press the countdown latch, and all threads stop.

```
Java
This post is part of our special coverage Global Voices 2011.

public static void main(String[] args) throws InterruptedException {
    // Create an ArrayBlockingQueue
    ArrayBlockingQueue<Integer> queue = new ArrayBlockingQueue<>(5);

    // Create producer threads
    Thread producer = new Thread(() -> {
        try {
            for (int i = 1; i <= 10; i++) {
                // Add elements to the queue, block waiting