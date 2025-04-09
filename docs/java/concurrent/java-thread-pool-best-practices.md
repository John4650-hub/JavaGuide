I don't...
Title: Java Thread Pool Best Practice
Category:
Tag:

- Java.
  I don't...

A brief summary of what I understand is to be noticed when it comes to the use of the thread pool, which does not seem to have a special article on the Internet.

#1. Correctly state the thread pool

**Thread pool shall be declared manually by the constructor function of `ThreadPoolExecutor` to avoid creating a thread pool using the `Executors` class, with OOM risk.**

The disadvantages of `Executors` for returning to the subject of the thread pool are as follows (detailed in later text):

- **`Fixed ThreadPool` and `Single ThreadExecutor`**: used to block queue `LinkedBlockingQueue`, with the default and maximum length of task queues being `Integer.MAX_VALUE`, which can be regarded as unbounded and may accumulate large numbers of requests leading to OOM.
- **`Cached ThreadPool`**: The use of `SynchronousQueue`, which allows the number of threads created to be `Integer.MAX_VALUE`, may create a large number of threads, leading to OOM.
- **`Scheduled ThreadPool` and `SingleThreadScheduledExecutor`**: The use of unbounded delay to block the queue `DelayedWorkQueue`, with a maximum duration of `Integer.MAX_VALUE`, may accumulate a large number of requests, leading to OOM.

In plain words: **The number is created using a lined queue.**

In addition to the reasons for avoiding OOM, the use of the two fast-threading pools provided by `Executors` is not recommended for the following reasons:

- Parameters that require the manual configuration of a thread pool based on the performance of its own machines, business scenarios, such as the number of core threads, the task queue used, saturation strategies, etc.
- We should show our thread pool names, which will help us locate problems.

#2. Monitor the thread pool operation

You can use some means to detect the operational status of a thread pool, such as the Actuator component in Spring Boot.

In addition to this, we can use the `ThreadPoolExecutor` related API to make a poor monitoring. As can be seen from the graph below, `ThreadPoolExecutor` provides the current number of threads and active threads in the thread pool, the number of tasks performed, the number of tasks in line, etc.

(https://oss.javaguide.cn/github/javaguide/java/concurrent/threadpool-methods-information.png)

Here's a simple Demo. `printThreadPoolStatus()` will print the number of threads, active threads, the number of tasks performed, and the number of tasks in the queue every second.

```Java
/**
 * Status of the thread pool
 * @param threadPool Thread Pool Object
 */
public void printThreadPoolStatus(ThreadPoolExecutor threadPool) {
    ScheduledExecutorService scheduledExecutorService = new ScheduledThreadPoolExecutor(1, new ThreadPoolExecutor.CallerRunsPolicy());
    log.info("==================================================");
    log.info("ThreadPool Size: [{}]", threadPool.getPoolSize());
    log.info("Active Threads: [{}]", threadPool.getActiveCount());
    log.info("Number of Tasks: [{}]", threadPool.getCompletedTaskCount());
    log.info("Number of Tasks in Queue: [{}]", threadPool.getQueue().size());
    log.info("==================================================");
}, 0, 1, TimeUnit.SECONDS);
```

#3. Suggest different thread pools for different types of operations

Many people in actual projects have a similar problem: **Do many of my projects need to have access to a thread pool, to define one for each or a common one?**

It is generally recommended that different operations use different thread pools, which are configured to the current thread pool in light of current operations, as the cooperation of different operations and the use of resources vary, with a focus on optimizing operations related to systemic bottlenecks.

**Let's look at another real case!** (The case is from: [An Online Case with Irregular Use of Thread Pools](https://heapdump.cn/article/646639)).

[Case code overview](https://oss.javaguide.cn/github/javaguide/java/concurrent/protection-accent-threadpool-sharing-example.png)

There could be a lock on the code. Why? Let me illustrate.

Imagine the extreme situation: if the core of our thread pool is **n**, the number of parent tasks (absorption tasks) is **n**, there are two sub-tasks under the parent task (absorption tasks), one of which has been completed and the other is placed in the task queue. As the parent task runs out of the core thread resources of the thread pool, the sub-task is blocked from the queue because it is unable to access the thread resources. The parent task awaits the completion of the sub-task, while the sub-task awaits the release of the resource of the parent task, which also results in a deadlock.

(https://oss.javaguide.cn
