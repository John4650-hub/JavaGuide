I don't...
Title: Completable Future
Category:
Tag:
- Java
I don't...

In the actual project, it is common that an interface may require multiple and varied data at the same time and then aggregate returns. For example: User requests for order information and may require simultaneous access to user information, commodity details, logistics information, commodity recommendations, etc.

If it is carried out in a serial (in order of each task), the interface will respond very slowly. Considering that most of these tasks are **unsequenced**, they could be carried out in **parallel**, i.e. when calling for commodity details, for example, one can also call for logistics information. The speed of response to the interface will be significantly optimized through the parallel implementation of multiple tasks.

(https://oss.javaguide.cn/github/javaguide/high-perform/serial-to-parallel.png)

Tasks may be organized for which there is a pre- and post-referral sequence.

(https://oss.javaguide.cn/github/javaguide/high-perform/serial-to-parallel2.png)

1. Access to commodity details and logistics information interfaces is only possible once user information is available.
2. Commodity referral interfaces can only be accessed after successful access to commodity details and logistics information.

The scenario may be used for multi-wire walker tasking (only here, for example, the data may not necessarily be a return and the interface may be split):

1. First page: For example, the front page of the technology community may need to be accompanied by a list of recommendations for articles, an ad bar, a list of articles, hot topics, etc.
2. Detailed pages: For example, for technical community articles, the detailed pages may require simultaneous access to author information, article details, article reviews, etc.
3. Statistical modules: Back-office statistical modules for the technical community, for example, may require simultaneous acquisition of information such as a collection of fans, article data (read, comment, collection).

For the Java program, the `CompletableFuture` introduced by Java 8 can help us organize multiple tasks, which are very powerful.

This article is a simple introduction to `CompletableFuture`, and shows you the API that is common to `CompletableFuture`.

# Future Introduction

The `Future` class is a typical application of the heterogeneity concept, which is mainly used in a number of scenarios that require time-consuming missions to avoid a process that waits in place for time-consuming tasks to be completed and is inefficient. Specifically, when we carry out a time-consuming task, we can entrust it to a sub-routine to take a walk, while we can do something else without waiting for the time-consuming task to be accomplished. When our work is done, we will obtain the results of the time-consuming mission through the `Future` class. As a result, the efficiency of the process was significantly improved.

This is actually the classic **Future mode** of multi-wire, which you can see as a design mode, with the core idea being an anecdotal, mainly in the multi-wire field, not unique to the Java language.

In Java, the `Future` class is a broad interface under the `java.util.concurrent` package, which defines five methods, including, inter alia, the following four functions:

- Cancelling the mission;
- To determine whether the mission has been cancelled;
- To determine whether the mandate has been fulfilled;
- Obtaining the results of the mission.

```Java
// V represents the type of return value for a task performed by Future
// Cancel Task Execution
// Successfully cancel return true, otherwise return false
boolean cancel(boolean mayInterruptIfRunning);
// Whether the mission has been cancelled
boolean isCancelled();
// Whether the task has been completed
boolean isDone();
// Access to mission performance results
V get() throws InterruptedException, ExecutionException;
// Release TimeoutException anomaly without returning the calculation at a specified time
V get(long timeout, TimeUnit unit) throws InterruptedException, ExecutionException, TimeoutException
```

A simple understanding is that I have a mission that I have submitted to the `Future` for processing. I can do anything I want to do during my mandate. Moreover, during this period I could also cancel the mandate and acquire the status of its implementation. After a while, I can `Future` there to get the results of the mission.

# Completable Future Introduction

`Future` has a number of limitations in its use in practice, such as the grouping that does not support different tasks, and the `get()` method of obtaining the result of the calculation is a blocking call.

Java 8 introduced the `CompletableFuture` class to address these deficiencies of `Future`. In addition to providing a more useful and powerful `Future` feature, the `CompletableFuture` also provides the capability of functional programming, a staggered task group (which can combine multiple different tasks into a full chain call).

Let us now briefly look at the definition of the `CompletableFuture` class.

```Java
public class CompletableFuture<V