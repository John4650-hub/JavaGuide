---
title: CompletableFuture Explained
category: Java
tag:
  - Java Concurrency
---

In actual projects, an interface may need to simultaneously retrieve various types of data and then summarize and return them. This scenario is quite common. For example, when a user requests order information, it may be necessary to simultaneously obtain user information, product details, logistics information, product recommendations, and other data.

If executed serially (executing each task in order), the response speed of the interface will be very slow. Considering that most of these tasks are **not sequentially dependent**, they can be **executed in parallel**. For instance, when calling to get product details, logistics information can be retrieved simultaneously. By executing multiple tasks in parallel, the response speed of the interface can be significantly optimized.

![](https://oss.javaguide.cn/github/javaguide/high-performance/serial-to-parallel.png)

For tasks that have a sequential calling relationship, task orchestration can be performed.

![](https://oss.javaguide.cn/github/javaguide/high-performance/serial-to-parallel2.png)

1. User information must be obtained before calling the product details and logistics information interfaces.
2. The product recommendation interface can only be called after successfully obtaining product details and logistics information.

There may be scenarios that require multi-threaded asynchronous task orchestration (this is just an example; the data may not be returned all at once and may involve splitting the interface):

1. Homepage: For example, the homepage of a technology community may need to simultaneously retrieve article recommendation lists, advertisements, article rankings, hot topics, and other information.
2. Detail page: For example, the article detail page of a technology community may need to simultaneously retrieve author information, article details, article comments, and other information.
3. Statistics module: For example, the backend statistics module of a technology community may need to simultaneously retrieve fan count summaries, article data (views, comments, favorites) summaries, and other information.

For Java programs, the `CompletableFuture` introduced in Java 8 can help us orchestrate multiple tasks and is very powerful.

This article is a simple introduction to `CompletableFuture`, showcasing commonly used APIs.

## Introduction to Future

The `Future` class is a typical application of asynchronous thinking, mainly used in scenarios that require executing time-consuming tasks, avoiding the program waiting idly for the completion of these tasks, which is inefficient. Specifically, when we execute a time-consuming task, we can hand this task over to a child thread for asynchronous execution while we can do other things without waiting for the time-consuming task to complete. Once we finish our tasks, we can retrieve the execution result of the time-consuming task through the `Future` class. This way, the execution efficiency of the program is significantly improved.

This is essentially the classic **Future pattern** in multithreading, which can be seen as a design pattern. The core idea is asynchronous calling, mainly used in the multithreading domain, and is not unique to the Java language.

In Java, the `Future` class is just a generic interface located in the `java.util.concurrent` package, which defines five methods, mainly including the following four functionalities:

- Cancel the task;
- Determine if the task has been canceled;
- Determine if the task has completed execution;
- Retrieve the task execution result.

```java
// V represents the type of the return value of the Future task
public interface Future<V> {
    // Cancel task execution
    // Returns true if successfully canceled, otherwise returns false
    boolean cancel(boolean mayInterruptIfRunning);
    // Determine if the task has been canceled
    boolean isCancelled();
    // Determine if the task has completed execution
    boolean isDone();
    // Retrieve the task execution result
    V get() throws InterruptedException, ExecutionException;
    // Throws TimeoutException if the result is not returned within the specified time
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

In simple terms: I have a task that I submitted to `Future` for processing. During the task execution, I can do anything I want. Additionally, during this time, I can also cancel the task and check the task's execution status. After a while, I can directly retrieve the task execution result from `Future`.

## Introduction to CompletableFuture

`Future` has some limitations in practical use, such as not supporting orchestration and combination of asynchronous tasks, and the `get()` method for obtaining computation results is a blocking call.

The `CompletableFuture` class introduced in Java 8 can solve these defects of `Future`. In addition to providing more user-friendly and powerful `Future` features, `CompletableFuture` also offers functional programming capabilities and asynchronous task orchestration and combination (allowing multiple asynchronous tasks to be chained together to form a complete chain call).

Let's take a brief look at the definition of the `CompletableFuture` class.

```java
public class CompletableFuture