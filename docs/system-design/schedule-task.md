I don't...
title: Java Time Job Details
Category: System Design
kon: "time"
Head:

- Meta.
- Name: keywords
  Quartz, Elastic-Job, XXL-JOB, PowerJob
- Meta.
- Name: description
  Grant: XXL-JOB was launched in 2015 and has been tested for many years. XXL-JOB is lightweight and very simple to use. Although performance bottlenecks exist, in the vast majority of cases they do not affect the basic needs of the enterprise. PowerJob is a new star in the field of distributed mission deployment, and its stability remains to be explored. ElasticJob, which is structured on Zookeeper, and XXL-JOB, which is based on a database, performance is slightly better than ElasticJob.
  I don't...

# Why Do You Need a Time Job?

Let's take a look at a few very common business scenarios:

1. A data backup is performed at 1 a.m. of a system.
1. An electrician platform is required to cancel an order automatically if the user has not paid for it for a single hour and a half.
1. A media aggregation platform that dynamically captures data from a website every 10 minutes for its own use.
1. A blog platform that supports the regular delivery of articles.
1. A fund platform that calculates the user's return on that day at regular intervals every night and sends the user's latest data.
1. ...

These scenarios often require us to do something at a given time, that is, to do something on time or late.

- Timed tasks: perform specific tasks at specified points in time, such as 8 a.m. per day, 3 p.m. per week, etc. Timed tasks can be used for some cyclical tasks, such as data backup, log cleaning, report generation, etc.
- Delayed tasks: Certain tasks are performed after a certain delay, e.g. 10 minutes, 3 hours, etc. Delayed missions can be used to perform some different work, such as cancellation of orders, rollover of notices, red bag withdrawal, etc.

While there is a difference in the context of their application, the core idea is to schedule the implementation of mandates at some point in the future in order to achieve the desired dispatch effect.

# A Single Timer

# Timer

`java.util.Timer` is a timed way of achieving a mandate that JDK 1.3 has begun to support.

The `Timer` internally uses a class timed task called `TaskQueue`, which is a priority queue based on the minimum stacks. `TaskQueue` will rank tasks according to the size of the mission from the next performance time and will ensure that the tasks at the top are carried out first. So when you need a job, you just have to take out the top of the job and run it!

`Timer` is simpler to use, and in the following way we can create a time-bound mission after 1s.

```Java
// Example code:
TimerTask task = new TimerTask() {
    @Override
    public void run() {
        System.out.println("Current Time: " + new Date() + "\n" +
                "Line name: " + Thread.currentThread().getName());
    }
};
System.out.println("Current Time: " + new Date() + "\n" +
        "Line name: " + Thread.currentThread().getName());
Timer timer = new Timer();
Long delay = 1000L;
timer.schedule(task, delay);
```

/ Output:
Current time: Fri May 28 15:18:47 CST 2021
Thread name: Main
Current time: Fri May 28 15:18:48 CST 2021
Thread name: Timer

````

However, there are more deficiencies, such as a `Timer` thread, which results in the `Timer` mission being carried out in a series of lines, with the impact on other tasks (very poor performance) of a mission being carried out over a long period of time, and the direct suspension of the mission in the event of an anomaly (`Timer` captures only the `InterruptedException`).

There is a note in the category `Timer` that reads:

```JAVA
// This class does not offer real-time
// Asks using the <tt>Object.wait.
// Java 5.0 introduced the {code java.util.concurrent} package and
// One of the concurrency utilities therein is the
// java.util.concurrent.ScheduledThreadPoolExecutor, which is a new pool for scheduling
// Combining, as it allows multiple services, accepts
// Time numbers, and doesn't reflect subclassing
// implement code Runnable. Configuring code
// With one lead makes it interesting to
// code time.
````

It is presumably: `ScheduledThreadPoolExecutor` supports multi-threaded timed tasks and is more powerful and a substitute for `Timer`.

# ScheduledExecutor Service

`ScheduledExecutorService` is an interface with multiple categories of realization, more commonly known as \`
