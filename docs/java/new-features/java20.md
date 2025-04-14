---
title: Overview of Java 20 New Features
category: Java
tag:
  - New Features in Java
---

JDK 20 was released on March 21, 2023, as a non-long-term support version.

According to the development plan, the next LTS version will be JDK 21, which is scheduled for release in September 2023.

![](https://oss.javaguide.cn/github/javaguide/java/new-features/640.png)

JDK 20 includes 7 new features:

- [JEP 429: Scoped Values (First Incubation)](https://openjdk.org/jeps/429)
- [JEP 432: Record Patterns (Second Preview)](https://openjdk.org/jeps/432)
- [JEP 433: Switch Pattern Matching (Fourth Preview)](https://openjdk.org/jeps/433)
- [JEP 434: Foreign Function & Memory API (Second Preview)](https://openjdk.org/jeps/434)
- [JEP 436: Virtual Threads (Second Preview)](https://openjdk.org/jeps/436)
- [JEP 437: Structured Concurrency (Second Incubation)](https://openjdk.org/jeps/437)
- [JEP 438: Vector API (Fifth Incubation)](https://openjdk.org/jeps/438)

## JEP 429: Scoped Values (First Incubation)

Scoped Values allow sharing immutable data within and across threads, offering advantages over thread-local variables, especially when dealing with a large number of virtual threads.

```java
final static ScopedValue<...> V = new ScopedValue<>();

// In some method
ScopedValue.where(V, <value>)
           .run(() -> { ... V.get() ... call methods ... });

// In a method called directly or indirectly from the lambda expression
... V.get() ...
```

Scoped Values enable safe and effective data sharing between components in large programs without relying on method parameters.

For a detailed introduction to Scoped Values, it is recommended to read the [Scoped Values FAQ](https://www.happycoders.eu/java/scoped-values/).

## JEP 432: Record Patterns (Second Preview)

Record Patterns deconstruct the values of records, making it easier to extract data from Record Classes. Additionally, they can be used in conjunction with nested record patterns and type patterns to enable powerful, declarative, and composable forms of data navigation and processing.

Record Patterns cannot be used in isolation but must be combined with instanceof or switch pattern matching.

Here’s a simple demonstration using instanceof:

Define a record class:

```java
record Shape(String type, long unit){}
```

Before Record Patterns:

```java
Shape circle = new Shape("Circle", 10);
if (circle instanceof Shape shape) {
  System.out.println("Area of " + shape.type() + " is : " + Math.PI * Math.pow(shape.unit(), 2));
}
```

With Record Patterns:

```java
Shape circle = new Shape("Circle", 10);
if (circle instanceof Shape(String type, long unit)) {
  System.out.println("Area of " + type + " is : " + Math.PI * Math.pow(unit, 2));
}
```

Now let's look at how Record Patterns work with switch.

Define some classes:

```java
interface Shape {}
record Circle(double radius) implements Shape { }
record Square(double side) implements Shape { }
record Rectangle(double length, double width) implements Shape { }
```

Before Record Patterns:

```java
Shape shape = new Circle(10);
switch (shape) {
    case Circle c:
        System.out.println("The shape is Circle with area: " + Math.PI * c.radius() * c.radius());
        break;

    case Square s:
        System.out.println("The shape is Square with area: " + s.side() * s.side());
        break;

    case Rectangle r:
        System.out.println("The shape is Rectangle with area: + " + r.length() * r.width());
        break;

    default:
        System.out.println("Unknown Shape");
        break;
}
```

With Record Patterns:

```java
Shape shape = new Circle(10);
switch(shape) {
  case Circle(double radius):
    System.out.println("The shape is Circle with area: " + Math.PI * radius * radius);
    break;

  case Square(double side):
    System.out.println("The shape is Square with area: " + side * side);
    break;

  case Rectangle(double length, double width):
    System.out.println("The shape is Rectangle with area: + " + length * width);
    break;

  default:
    System.out.println("Unknown Shape");
    break;
}
```

Record Patterns can eliminate unnecessary casts, making the code more concise and readable. Additionally, when using Record Patterns, there’s no need to worry about `null` or `NullPointerException`, thus enhancing code safety and reliability.

Record Patterns had their first preview in Java 19, proposed by [JEP 405](https://openjdk.org/jeps/405). The second preview was introduced in JDK 20, proposed by [JEP 432](https://openjdk.org/jeps/432), which included improvements such as:

- Support for type parameter inference for general record patterns,
- Support for the use of record patterns in the enhanced statement header `for`,
- Removal of support for named record patterns.

[!NOTE]
Do not confuse Record Patterns with Record Classes, which were formally introduced in [JDK 16](./java16.md).
## JEP 433: Switch Pattern Matching (Fourth Preview)

Just as `instanceof` received automatic type matching capabilities, `switch` has also followed suit.

Example of `instanceof`:

```java
// Old code
if (o instanceof String) {
    String s = (String)o;
    ... use s ...
}

// New code
if (o instanceof String s) {
    ... use s ...
}
```

Example of `switch`:

```java
// Old code
static String formatter(Object o) {
    String formatted = "unknown";
    if (o instanceof Integer i) {
        formatted = String.format("int %d", i);
    } else if (o instanceof Long l) {
        formatted = String.format("long %d", l);
    } else if (o instanceof Double d) {
        formatted = String.format("double %f", d);
    } else if (o instanceof String s) {
        formatted = String.format("String %s", s);
    }
    return formatted;
}

// New code
static String formatterPatternSwitch(Object o) {
    return switch (o) {
        case Integer i -> String.format("int %d", i);
        case Long l    -> String.format("long %d", l);
        case Double d  -> String.format("double %f", d);
        case String s  -> String.format("String %s", s);
        default        -> o.toString();
    };
}
```

Switch pattern matching had previews in Java 17, Java 18, and Java 19, with Java 20 being the fourth preview. Each preview typically includes minor improvements, which won't be detailed here.

## JEP 434: Foreign Function and Memory API (Second Preview)

This API allows Java programs to interoperate with code and data outside the Java runtime. By efficiently calling external functions (i.e., code outside the JVM) and safely accessing external memory (i.e., memory not managed by the JVM), the API allows Java programs to call native libraries and handle native data without the dangers and fragility associated with JNI.

The Foreign Function and Memory API underwent its first incubation round in Java 17, proposed by [JEP 412](https://openjdk.java.net/jeps/412). A second incubation round occurred in Java 18, proposed by [JEP 419](https://openjdk.org/jeps/419). The first preview was introduced in Java 19, proposed by [JEP 424](https://openjdk.java.net/jeps/424).

In JDK 20, the second preview, proposed by [JEP 434](https://openjdk.java.net/jeps/434), included improvements such as:

- Uniformity of the `MemorySegment` and `MemoryAddress` abstractions,
- Enhanced hierarchy for `MemoryLayout`,
- Splitting `MemorySession` into `Arena` and `SegmentScope` to facilitate segment sharing across maintenance boundaries.

I have provided a detailed introduction to the Foreign Function and Memory API in the [Overview of New Features in Java 19](./java19.md), so I will not provide additional details here.

## JEP 436: Virtual Threads (Second Preview)

Virtual threads are lightweight threads implemented by the JDK rather than the OS, scheduled by the JVM. Many virtual threads can share the same operating system thread, allowing the number of virtual threads to far exceed the number of operating system threads.

Before the introduction of virtual threads, the `java.lang.Thread` package supported what is known as platform threads, which are the threads we have been using prior to virtual threads. The JVM scheduler manages virtual threads through platform threads (carrier threads), meaning a platform thread can execute different virtual threads at different times (multiple virtual threads mounted on a single platform thread), allowing the platform thread to switch back and forth between executing different virtual threads when one is blocked or waiting.

The relationship between virtual threads, platform threads, and system kernel threads is illustrated below (Image source: [How to Use Java 19 Virtual Threads](https://medium.com/javarevisited/how-to-use-java-19-virtual-threads-c16a32bad5f7)):

![Relationship Between Virtual Threads, Platform Threads, and System Kernel Threads](https://oss.javaguide.cn/github/javaguide/java/new-features/virtual-threads-platform-threads-kernel-threads-relationship.png)

For a further explanation of the relationship between platform threads and system kernel threads, in mainstream operating systems like Windows and Linux, Java threads utilize a one-to-one threading model, meaning one platform thread corresponds to one system kernel thread. The Solaris system is a special case, as the HotSpot VM supports both many-to-many and one-to-one threading models on Solaris. For specific information, refer to R's answer: [Is the thread model in the JVM user-level?](https://www.zhihu.com/question/23096638/answer/29617153).

In comparison to platform threads, virtual threads are inexpensive and lightweight, being destroyed immediately after use, meaning they don't need to be reused or pooled; each task can have its own dedicated virtual thread to execute. Virtual threads use pausing and resuming to switch between threads, avoiding the overhead of context switching, while maintaining the benefits of multithreading and simplifying the complexity of high-concurrency programs, effectively reducing the workload involved in writing, maintaining, and observing high-throughput concurrent applications.

Virtual threads have already proven to be quite useful in other multithreading languages, such as Goroutines in Go and processes in Erlang.

There is a discussion about Java 19 virtual threads on Zhihu; if interested, you can check it out: <https://www.zhihu.com/question/536743167>.

For detailed interpretations and principles of Java virtual threads, you can refer to the following articles:

- [Virtual Threads - A Simplified Introduction](https://javaguide.cn/java/concurrent/virtual-thread.html)
- [Java 19 Officially GA! See How Virtual Threads Greatly Improve System Throughput](https://mp.weixin.qq.com/s/yyApBXxpXxVwttr01Hld6Q)
- [Virtual Threads - Diving into VirtualThread Source Code](https://www.cnblogs.com/throwable/p/16758997.html)

Virtual threads had their first preview in Java 19, proposed by [JEP 425](https://openjdk.org/jeps/425). In JDK 20, it underwent a second preview with some minor changes, which will not be elaborated on here.

Finally, let's look at four ways to create virtual threads:

```java
// 1. Create using Thread.ofVirtual()
Runnable fn = () -> {
  // your code here
};

Thread thread = Thread.ofVirtual(fn)
                      .start();

// 2. Create using Thread.startVirtualThread()
Thread thread = Thread.startVirtualThread(() -> {
  // your code here
});

// 3. Create using Executors.newVirtualThreadPerTaskExecutor()
var executorService = Executors.newVirtualThreadPerTaskExecutor();

executorService.submit(() -> {
  // your code here
});

class CustomThread implements Runnable {
  @Override
  public void run() {
    System.out.println("CustomThread run");
  }
}

// 4. Create using ThreadFactory
CustomThread customThread = new CustomThread();
// Get the thread factory
ThreadFactory factory = Thread.ofVirtual().factory();
// Create a virtual thread
Thread thread = factory.newThread(customThread);
// Start the thread
thread.start();
```

The four methods of creating virtual threads demonstrate that the officials have made efforts to lower the barrier for virtual threads by reusing the existing `Thread` class, allowing for a smooth transition to the use of virtual threads.

## JEP 437: Structured Concurrency (Second Incubation)

Java 19 introduced structured concurrency, a multi-threading programming approach aimed at simplifying multi-threading through a structured concurrency API, rather than replacing `java.util.concurrent`, which is currently in the incubation phase.

Structured concurrency treats multiple tasks running in different threads as a single unit of work, simplifying error handling, enhancing reliability, and improving observability. In other words, structured concurrency retains the readability, maintainability, and observability of single-threaded code.

The basic API for structured concurrency is [`StructuredTaskScope`](https://download.java.net/java/early_access/loom/docs/api/jdk.incubator.concurrent/jdk/incubator/concurrent/StructuredTaskScope.html). `StructuredTaskScope` supports splitting tasks into multiple concurrent subtasks that run in their own threads, and these subtasks must complete before the main task can continue.

Here is a basic usage example of `StructuredTaskScope`:

```java
try (var scope = new StructuredTaskScope<Object>()) {
    // Use fork method to derive threads to execute subtasks
    Future<Integer> future1 = scope.fork(task1);
    Future<String> future2 = scope.fork(task2);
    // Wait for the threads to complete
    scope.join();
    // Result processing may include handling or rethrowing exceptions
    ... process results/exceptions ...
} // close
```

Structured concurrency is particularly well-suited for virtual threads, which are lightweight threads implemented by the JDK. Many virtual threads can share the same operating system thread, allowing for a very large number of virtual threads.

The only change to structured concurrency in JDK 20 is that it now supports inheriting scoped values created within the task scope in the `StructuredTaskScope`, simplifying the sharing of immutable data across threads. This is detailed in [JEP 429](https://openjdk.org/jeps/429).

## JEP 438: Vector API (Fifth Incubation)

Vector computation consists of a series of operations on vectors. The Vector API is used to express vector computations that can be reliably compiled at runtime to the best vector instructions available on supported CPU architectures, thus achieving better performance than equivalent scalar computations.

The goal of the Vector API is to provide users with a straightforward and platform-independent way to express a wide range of vector computations.

The Vector API was initially proposed by [JEP 338](https://openjdk.java.net/jeps/338) and integrated into Java 16 as an [incubating API](http://openjdk.java.net/jeps/11). The second incubation round was proposed by [JEP 414](https://openjdk.java.net/jeps/414) and integrated into Java 17, the third incubation round by [JEP 417](https://openjdk.java.net/jeps/417) and integrated into Java 18, and the fourth round by [JEP 426](https://openjdk.java.net/jeps/426) and integrated into Java 19.

The fifth incubation in JDK 20 did not fundamentally change the Vector API but involved some bug fixes and performance enhancements, as detailed in [JEP 438](https://openjdk.java.net/jeps/438).
