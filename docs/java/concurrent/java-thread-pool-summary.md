I don't...
Title: Java Thread Pool Detail
Category:
Tag:

- Java.
  I don't...

- markdownlint-disable MD024--

Pooling is something that you must have seen a lot of times. Thread pool, database connection pool, HTTP connection pool, and so on. The idea of pool technology is primarily to reduce the consumption of resources per acquisition and to increase their utilization.

In this article, I will elaborate on the basic concept of the thread pool and its core principles.

# Thread Pool Introduction

By definition, a thread pool is a resource pool that manages a series of threads, providing a way to limit and manage the thread resources. Some basic statistical information, such as the number of tasks performed, is also maintained in each loop.

Here's a summary of the benefits of using a thread pool, using part of Java's Art with Programming:

- **Reduce resource consumption**. Reducing the consumption caused by the creation and destruction of threads by reusing created threads.
- **Increased response**. When a task arrives, it can be carried out without waiting until a thread is created.
- **Improved resource management**. Threads are scarce resources and, if created without restriction, not only consume system resources but also reduce system stability, and the use of thread pools allows for uniform distribution, optimization, and monitoring.

**Thread pools are generally used to perform multiple unrelated and time-consuming tasks, and in the absence of multiple threads, the sequence of tasks is carried out. If they are used, multiple unrelated tasks can be performed simultaneously.**

# Executor Framework Introduction

The `Executor` framework was introduced after Java 5 and, after Java 5, the `Executor` is a better way to start the thread than the `start` method of `Thread`, and, in addition to being easier to manage and more efficient (achieved by a thread pool, cost-saving), there is a key point: helping to avoid the escape problem.

> This escape means that other threads hold references to the object before the construction function returns, and the way to call objects that have not yet been fully constructed may give rise to questionable errors.

The `Executor` framework includes not only the management of a thread pool but also the provision of thread factories, queues, and rejection strategies, and the `Executor` framework makes co-programming simpler.

The `Executor` framework structure consists of three main components:

**1. Task (`Runnable` / `Callable`)**

`Runnable` or `Callable`. The class of realization can be executed by `ThreadPoolExecutor` or `ScheduledThreadPoolExecutor`.

**2. Implementation of the Task (`Executor`)**

As shown in the figure below, including the core interface of the task implementation mechanism `Executor` and the `ExecutorService` interface inherited from the `Executor` interface. `ThreadPoolExecutor` and `ScheduledThreadPoolExecutor` are the two key classes that have implemented the `ExecutorService` interface.

(https://oss.javaguide.cn/github/javaguide/java/concurent/executor-class-diagram.png)

There are many bottom-level relationships here, but, in fact, we need to pay more attention to the class `ThreadPoolExecutor`, which is still very high in the course of our actual use of the thread pool.

**Note:** By looking at the source code `ScheduledThreadPoolExecutor`, we found that `ScheduledThreadPoolExecutor` had in fact inherited `ThreadPoolExecutor` and achieved `ScheduledExecutorService`, while `ScheduledExecutorService` had also achieved `ExecutorService`, as shown in the graph of relationships above.

The `ThreadPoolExecutor` class description:

```Java
// AbstractExecutorService
public class ThreadPoolExecutor {
    // Implementation details...
}
```

`ScheduledThreadPoolExecutor` description:

```Java
// ScheduledExecutorService extends ExecutorService
public class ScheduledThreadPoolExecutor extends ThreadPoolExecutor implements ScheduledExecutorService {
    // Implementation details...
}
```

**3. Results of the Task Execution (`Future`)**

The `Future` interface and the `Future` interface realization class `FutureTask` can represent the results of the task execution.

When we submit a task, it returns a `FutureTask` object when calling the `submit()` method.

**Map of the use of the `Executor` framework**:

![Executor framework usage diagram](./images/java-thread-pool-summary/Executor-framework-usage-diagram.png)

The main thread first creates a task object for the `Runnable` or `Callable` interface.
2\. The object that was created to implement the `Runnable` / `Callable` interface will be delivered directly to the `ExecutorService` for execution: `ExecutorService.execute(Runnable command)` or may also be submitted to the `ExecutorService` for execution (`ExecutorService.submit(Runnable task)`).
3\. If `ExecutorService.submit(...)`, `ExecutorService` will return an object that implements the `Future` interface (we have just mentioned the difference between the `execute()` method and `submit()` method, `submit()` will return a `FutureTask`
