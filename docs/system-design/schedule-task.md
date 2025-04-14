---
title: Detailed Explanation of Java Scheduled Tasks
category: System Design
icon: time
head:
  -   - meta
      - name: keywords
        content: Scheduled Tasks, Quartz, Elastic-Job, XXL-JOB, PowerJob
  -   - meta
      - name: description
        content: XXL-JOB was launched in 2015 and has been tested over many years. XXL-JOB is lightweight and very easy to use. Although there are performance bottlenecks, in most cases, it does not affect the basic needs of enterprises. PowerJob is a rising star in the field of distributed task scheduling, and its stability still needs further investigation. ElasticJob, due to its architecture design based on Zookeeper, has slightly better performance than XXL-JOB, which is based on a database.
---

## Why Do We Need Scheduled Tasks?

Let's look at a few very common business scenarios:

1. A system needs to perform data backup at 1 AM.
1. An e-commerce platform needs to automatically cancel orders that have not been paid within half an hour.
1. A media aggregation platform dynamically fetches data from a certain website every 10 minutes for its own use.
1. A blogging platform supports scheduled article sending.
1. A fund platform calculates users' daily earnings every night and pushes the latest data to users.
1. ……

These scenarios often require us to perform a certain task at a specific time, which means scheduling or delaying tasks.

- Scheduled Task: Executes a specific task at a designated time, such as 8 AM every day or 3 PM every Monday. Scheduled tasks can be used for periodic work, such as data backup, log cleanup, report generation, etc.
- Delayed Task: Executes a specific task after a certain delay, such as after 10 minutes or 3 hours. Delayed tasks can be used for asynchronous work, such as order cancellation, push notifications, red envelope withdrawals, etc.

Although the applicable scenarios for both differ, their core idea is to schedule the execution time of tasks at some point in the future to achieve the desired scheduling effect.

## Single Machine Scheduled Tasks

### Timer

`java.util.Timer` is a way to implement scheduled tasks that has been supported since JDK 1.3.

`Timer` internally uses a class called `TaskQueue` to store scheduled tasks, which is a priority queue implemented based on a min-heap. `TaskQueue` sorts tasks according to the time until their next execution, ensuring that the task at the top of the heap is executed first. Thus, when a task needs to be executed, we only need to take out the task at the top of the heap to run it!

Using `Timer` is relatively simple; we can create a scheduled task that executes after 1 second as follows:

```java
// Example code:
TimerTask task = new TimerTask() {
    public void run() {
        System.out.println("Current time: " + new Date() + "n" +
                "Thread name: " + Thread.currentThread().getName());
    }
};
System.out.println("Current time: " + new Date() + "n" +
        "Thread name: " + Thread.currentThread().getName());
Timer timer = new Timer("Timer");
long delay = 1000L;
timer.schedule(task, delay);


// Output:
Current time: Fri May 28 15:18:47 CST 2021nThread name: main
Current time: Fri May 28 15:18:48 CST 2021nThread name: Timer
```

However, it has many drawbacks, such as one `Timer` using one thread, which means that tasks can only be executed serially. If one task takes too long, it will affect the execution of other tasks (very poor performance). Additionally, if an exception occurs, the task stops directly (the `Timer` only catches `InterruptedException`).

A comment on the `Timer` class states:

```JAVA
 * This class does not offer real-time guarantees: it schedules
 * tasks using the <tt>Object.wait(long)</tt> method.
 * Java 5.0 introduced the {@code java.util.concurrent} package and
 * one of the concurrency utilities therein is the {@link
 * java.util.concurrent.ScheduledThreadPoolExecutor
 * ScheduledThreadPoolExecutor} which is a thread pool for repeatedly
 * executing tasks at a given rate or delay.  It is effectively a more
 * versatile replacement for the {@code Timer}/{@code TimerTask}
 * combination, as it allows multiple service threads, accepts various
 * time units, and doesn't require subclassing {@code TimerTask} (just
 * implement {@code Runnable}).  Configuring {@code
 * ScheduledThreadPoolExecutor} with one thread makes it equivalent to
 * {@code Timer}.
```

The general idea is that `ScheduledThreadPoolExecutor` supports multi-threaded execution of scheduled tasks and is more powerful, making it a replacement for `Timer`.
