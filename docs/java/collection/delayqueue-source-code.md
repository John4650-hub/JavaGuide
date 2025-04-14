---
title: DelayQueue Source Code Analysis
category: Java
tag:
  - Java Collections
---

## Introduction to DelayQueue

`DelayQueue` is a delay queue provided by the JUC package (`java.util.concurrent`) for implementing delayed tasks, such as automatically canceling an order if it has not been paid within 15 minutes. It is a type of `BlockingQueue`, and its underlying implementation is an unbounded queue based on `PriorityQueue`, which is thread-safe. For more information on `PriorityQueue`, you can refer to the article I wrote: [PriorityQueue Source Code Analysis](./priorityqueue-source-code.md).

![Implementation Classes of BlockingQueue](https://oss.javaguide.cn/github/javaguide/java/collection/blocking-queue-hierarchy.png)

The elements stored in `DelayQueue` must implement the `Delayed` interface and override the `getDelay()` method (to calculate whether they have expired).

```java
public interface Delayed extends Comparable<Delayed> {
    long getDelay(TimeUnit unit);
}
```

By default, `DelayQueue` arranges tasks in ascending order of their expiration time. An element can only be removed from the queue when it has expired (i.e., when the return value of `getDelay()` is less than or equal to 0).

## History of DelayQueue

- `DelayQueue` was first introduced in Java 5 as part of the `java.util.concurrent` package to support time-based task scheduling and cache expiration scenarios. This version only supported the implementation of delay functionality and did not address thread safety issues.
- In Java 6, the implementation of `DelayQueue` was optimized by using `ReentrantLock` and `Condition` to solve thread safety and inter-thread interaction efficiency, improving its performance and reliability.
- In Java 7, the implementation of `DelayQueue` was further optimized by using CAS operations for adding and removing elements, enhancing its concurrent operation performance.
- In Java 8, there were no major changes to the implementation of `DelayQueue`, but new time classes such as `Duration` and `Instant` were introduced in the `java.time` package, making it more convenient and flexible to use `DelayQueue` for time-based scheduling.
- In Java 9, there were some minor improvements to the implementation of `DelayQueue`, mainly optimizing and streamlining the code.

Overall, the development history of `DelayQueue` mainly involves optimizing its implementation and improving its performance and reliability, making it more suitable for time-based scheduling and cache expiration scenarios.

## Common Usage Scenarios of DelayQueue

We hope that tasks can be executed at our expected times. For example, if we submit three tasks that require execution after 1s, 2s, and 3s respectively, even if they are added in a random order, the task scheduled for 1s will execute on time.

![Delayed Tasks](https://oss.javaguide.cn/github/javaguide/java/collection/delayed-task.png)

To achieve this, we can use `DelayQueue`. First, we need to implement `Delayed` by creating a `DelayedTask` class, implementing the `getDelay` method and the priority comparison `compareTo`.

```java
/**
 * Delayed Task
 */
public class DelayedTask implements Delayed {
    /**
     * Task expiration time
     */
    private long executeTime;
    /**
     * Task
     */
    private Runnable task;

    public DelayedTask(long delay, Runnable task) {
        this.executeTime = System.currentTimeMillis() + delay;
        this.task = task;
    }

    /**
     * Check how long until the current task expires
     * @param unit
     * @return
     */
    @Override
    public long getDelay(TimeUnit unit) {
        return unit.convert(executeTime - System.currentTimeMillis(), TimeUnit.MILLISECONDS);
    }

    /**
     * The delay queue requires tasks to be enqueued in ascending order of expiration time, so we need to implement compareTo for expiration time comparison
     * @param o
     * @return
     */
    @Override
    public int compareTo(Delayed o) {
        return Long.compare(this.executeTime, ((DelayedTask) o).executeTime);
    }

    public void execute() {
        task.run();
    }
}
```

After encapsulating the task, using it is straightforward: set how long until expiration and submit the task to the delay queue.

```java
// Create a delay queue and add tasks
DelayQueue<DelayedTask> delayQueue = new DelayQueue<>();

// Add tasks with expiration times of 1s, 2s, and 3s respectively
delayQueue.add(new DelayedTask(2000, () -> System.out.println("Task 2")));
delayQueue.add(new DelayedTask(1000, () -> System.out.println("Task 1")));
delayQueue.add(new DelayedTask(3000, () -> System.out.println("Task 3")));

//