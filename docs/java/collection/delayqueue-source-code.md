I don't...
title: DelayQueue Source Analysis
Category:
Tag:

- Java.
  I don't...

# DelayQueue Profile

`DelayQueue` is a `java.util.concurrent` package provided to us by the `DelayQueue`, which is used to achieve delayed tasks such as 15 minutes of unpaid direct cancellation of orders. It is one of the `BlockingQueue`, and the bottom is an open line based on `PriorityQueue`, which is linearly secure. This article on `PriorityQueue` can be consulted: [PriorityQueue Source Analysis](./priorityQueue-source-code.md).

![DelayQueue Hierarchy](https://oss.javaguide.cn/github/javaguide/java/collection/blocking-queue-hierarchy.png)

The elements stored in the `DelayQueue` must implement the `Delayed` interface and need to override the `getDelay()` method (the calculation is due or not).

```Java
I'm not sure I'm going to be able to do that.
I'll get Delay.
♪ I'm sorry ♪
```

By default, `DelayQueue` will organize the tasks in the order of maturity. Only when the element expires (`getDelay()` returns a value less than 0) can it be removed from the queue.

# DelayQueue

- `DelayQueue` was first introduced in Java 5 as part of the `java.util.concurrent` package to support time-based scenarios such as time-based task scheduling and cache elimination, which only supports the achievement of delayed functionality and has not resolved the problem of linear security.
- In Java 6, the `DelayQueue` was optimized to improve its performance and reliability by using the `ReentrantLock` and `Condition` to resolve the efficiency of the security of the thread and the interaction between it.
- In Java 7, `DelayQueue` was further optimized to improve the cooperation and operation of elements by using CAS.
- In Java 8, the implementation of `DelayQueue` has not changed significantly, but new time classes such as `Duration` and `Instant` have been introduced in the `java.time` package, making it easier and more flexible to use `DelayQueue` for time-based scheduling.
- In Java 9, `DelayQueue` was achieved with minor improvements, mainly some optimization and streamlining of codes.

In general, the `DelayQueue` development history has been geared to making it more relevant to time-based scheduling and cache deletions by optimizing its implementation and improving its performance and reliability.

# DelayQueue Common Use Case Examples

We hope that the task can be carried out in accordance with the time we have expected, for example, by submitting three missions requiring 1s, 2s, and 3s, respectively, even if they are added in an irregular sequence, and by requesting 1s on time.

![Delayed Task](https://oss.javaguide.cn/github/javaguide/java/collection/delayed-task.png)

We can do this using the `DelayQueue`, so we need to first implement `Delayed` to achieve `DelayedTask`, to implement `getDelay`, and to prioritize comparison `compareTo`.

```Java
/**
 * Delayed Task
 */
public class DelayedTask implements Delayed {
    private final long executeTime;
    private final Runnable task;

    public DelayedTask(long delay, Runnable task) {
        this.executeTime = System.currentTimeMillis() + delay;
        this.task = task;
    }

    /**
     * See how long the current task will expire
     * @param unit
     * @return
     */
    @Override
    public long getDelay(TimeUnit unit) {
        return unit.convert(executeTime - System.currentTimeMillis(), TimeUnit.MILLISECONDS);
    }

    /**
     * Delaying Queue requires a comparison of due time
     * @param o
     * @return
     */
    @Override
    public int compareTo(Delayed o) {
        return Long.compare(this.executeTime, ((DelayedTask) o).executeTime);
    }
}
```

When the cover of the mission is completed, it is simple to use, setting the time frame to expire and then submitting the task to the delayed queue.

```Java
// Create delayed queue and add tasks
DelayQueue<DelayedTask> delayQueue = new DelayQueue<>();

// Add 1s, 2s, 3s, respectively
delayQueue.add(new DelayedTask(2000, () -> System.out.println("Task 2")));
delayQueue.add(new DelayedTask(1000, () -> System.out.println("Task 1")));
delayQueue.add(new DelayedTask(3000, () -> System.out.println("Task 3")));

// Take out tasks and perform
while (!delayQueue.isEmpty()) {
    // Block access to the first expired task
    DelayedTask task = delayQueue.take();
    task.task.run();
}
```

As can be seen from the output results, even if the pen first refers to a task that is due to expire at 2s,
