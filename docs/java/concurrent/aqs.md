I don't...
title: AQS Details
Category:
Tag:

- Java.
  I don't...

- markdownlint-disable MD024--

# AQS Introduction

The full name of AQS is `AbstractQueuedSynchronizer`, which is translated to mean the abstract queue synchronizer. This category is under the `java.util.concurrent.locks` bag.

![AQS Image](https://oss.javaguide.cn/github/javaguide/AQS.png)

AQS is an abstract category that is used to build locks and synchronizers.

```Java
I'm not sure I'm going to be able to do that.
♪ I'm sorry ♪
```

AQS provides some universal functionality for building locks and synchronizers. Thus, the use of AQS allows for the simple and efficient construction of a wide range of synchronizers for applications, such as `ReentrantLock`, `Semaphore`, to which we refer, and others such as `ReentrantReadWriteLock`, `SynchronousQueue`, and others are based on AQS.

# AQS Method

When asked during interviews to share knowledge, most of them are asked, “Please explain your understanding of AQS principles.” The following is an example for you. Interviews are not a fallout, and you must join your minds, even if you cannot, to ensure that you can speak out rather than back.

## AQS Quick to Understand

Before really talking about AQS source code, there is a need to have a holistic understanding of AQS. This is the first time that AQS can be understood at a holistic level through a number of questions, in terms of the level of AQS throughout Java and in the distribution, and then in the course of learning the AQS source code, there is a better understanding of the relationship between the synchronizer and AQS.

What's the role of AQS?

AQS addresses the complexity of developers in achieving the synchronization. It provides a common framework for achieving various synchronizers, such as **re-lockable** (`ReentrantLock`), **signal volume** (`Semaphore`), and **countdown** (`CountDownLatch`). AQS hides the complex line management logic by encapsulating the bottom line synchronous mechanism, so that developers simply focus on specific synchronized logic.

In short, AQS is an abstract category that provides a common **implementation framework for synchronizers**. It defines **a common process for resource acquisition and release**, while the specific resource acquisition logic is achieved by rewriting templates by specific synchronizers. Thus, AQS can be considered as the **base “underground”** of the **sync, while the **specific “applications”** are based on AQS.**

### AQS: Why Use CLH to Lock the Queue Variant?

The CLH lock is an optimization based on **Spinlock**.

First of all, the problem of the spinlock: the `compareAndSet` (known as `CAS`) operation of the `compareAndSet` on an atomic variable continues to attempt to acquire the lock through the spinning. In the high-term scenario, multiple threads compete for the same atomic variable at the same time, and can easily cause the `CAS` operation of a linear range to fail for a long time, leading to **the problem of “hunger”** (some threads may never be able to obtain locks).

CLH locks are improved by introducing a queue to organize and compete lines:

- Each thread is added to the queue as a node and monitors the status of the previous node by spinning, rather than directly competing for the sharing of variables.
- Lines line up in order to ensure fairness, thus avoiding the problem of “hunger”.

AQS (AbstractQueuedSynchronizer) further optimizes the CLH lock to form its internal **CLH queue variant**. The main improvements are twofold:

1. **Spin + Block**: CLH locks wait for the release of the locks using a pure spin method, but a large number of spin operations will consume excessive CPU resources. AQS introduced a hybrid mechanism **Spin + Block**:
   - If a linear lock fails, a short spin will attempt to obtain the lock;
   - If it still fails, the thread will be blocked, waiting to wake up, thus reducing the waste of CPU.
1. **One-way queue to two-way queue**: CLH lock uses a one-way queue, where a node only knows the state of the front node, and when a node releases the lock, the subsequent node needs to be awakened through the queue. AQS changed the queue to a **two-way queue** with the addition of a `next` pointer, so that a node not only knows the front node but can also awaken the back node directly, thus simplifying the queue operation and increasing its wake-up efficiency.

### AQS Has Better Performance, Why?

Because `CAS` operations are used extensively within AQS.

AQS internally stores the waiting linear nodes through queues. Since the queue is a shared resource, in a multi-line scenario, simultaneous access to the queue is required.

Internal A
