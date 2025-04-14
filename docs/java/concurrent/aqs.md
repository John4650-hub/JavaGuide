---
title: AQS Explained
category: Java
tag:
  - Java Concurrency
---

## Introduction to AQS

AQS stands for `AbstractQueuedSynchronizer`, which translates to "Abstract Queue Synchronizer." This class is located in the `java.util.concurrent.locks` package.

![](https://oss.javaguide.cn/github/javaguide/AQS.png)

AQS is an abstract class primarily used to build locks and synchronizers.

```java
public abstract class AbstractQueuedSynchronizer extends AbstractOwnableSynchronizer implements java.io.Serializable {
}
```

AQS provides some common functionalities for building locks and synchronizers. Therefore, using AQS allows for the simple and efficient construction of a wide range of synchronizers, such as `ReentrantLock`, `Semaphore`, and others like `ReentrantReadWriteLock`, `SynchronousQueue`, etc., all of which are based on AQS.

## Principles of AQS

In interviews, when asked about concurrency knowledge, most candidates will be asked, "Can you explain your understanding of the principles of AQS?" Below is an example for reference. Interviews are not about rote memorization; you should incorporate your own thoughts. Even if you can't add your own ideas, ensure you can explain it in simple terms rather than just reciting it.

### Quick Overview of AQS

Before diving into the AQS source code, it's essential to have an overall understanding of AQS. Here, we will first recognize AQS from a broader perspective through several questions, understanding its position in the entire Java concurrency framework. This will help in better understanding the relationship between synchronizers and AQS during the study of AQS source code.

#### What is the role of AQS?

AQS addresses the complexity problem developers face when implementing synchronizers. It provides a general framework for implementing various synchronizers, such as **Reentrant Lock** (`ReentrantLock`), **Semaphore** (`Semaphore`), and **CountDownLatch** (`CountDownLatch`). By encapsulating the underlying thread synchronization mechanisms, AQS hides the complex thread management logic, allowing developers to focus solely on the specific synchronization logic.

In simple terms, AQS is an abstract class that provides a general **execution framework** for synchronizers. It defines a **common process for resource acquisition and release**, while the specific resource acquisition logic is implemented by specific synchronizers through overriding template methods. Therefore, AQS can be seen as the **basic "foundation"** of synchronizers, while synchronizers are the **specific "applications"** built on top of AQS.

#### Why does AQS use a variant of the CLH lock queue?

The CLH lock is an optimized implementation based on **spin locks**.

First, let's discuss the problems with spin locks: Spin locks attempt to acquire a lock by continuously executing `compareAndSet` (abbreviated as `CAS`) operations on an atomic variable. In high-concurrency scenarios, multiple threads may compete for the same atomic variable simultaneously, leading to a situation where a particular thread's `CAS` operation fails for an extended period, resulting in the **"starvation problem"** (some threads may never acquire the lock).

The CLH lock improves upon spin locks by introducing a queue to organize competing threads:

- Each thread joins the queue as a node and monitors the state of the previous thread node through spinning, rather than directly competing for a shared variable.
- Threads are queued in order, ensuring fairness and avoiding the "starvation" problem.

AQS (AbstractQueuedSynchronizer) further optimizes the CLH lock, forming its internal **CLH queue variant**. The main improvements are as follows:

1. **Spin + Block**: The CLH lock uses pure spinning to wait for the lock to be released, but excessive spinning can consume too many CPU resources. AQS introduces a **spin + block** hybrid mechanism:
   - If a thread fails to acquire the lock, it will first attempt to spin briefly to acquire the lock;
   - If it still fails, the thread will enter a blocked state, waiting to be awakened, thus reducing CPU waste.
1. **Unidirectional Queue to Bidirectional Queue**: The CLH lock uses a unidirectional queue, where nodes only know the state of their predecessor. When a node releases the lock, it must wake up subsequent nodes through the queue. AQS changes the queue to a **bidirectional queue**, adding a `next` pointer, allowing nodes to know not only their predecessor but also directly wake up their successor, simplifying queue operations and improving wake-up efficiency.

#### Why does AQS perform well?

AQS performs well because it heavily utilizes `CAS` operations.

Internally, AQS uses a queue to store waiting thread nodes. Since the queue is a shared resource, it needs to ensure synchronized access in a multithreaded environment.

AQS controls the synchronized access to the queue through `CAS` operations, which are primarily used to ensure the concurrent safety of two operations: `queue initialization` and
