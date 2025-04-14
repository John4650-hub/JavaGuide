---
title: Understanding AQS Principles and Applications through the Implementation of ReentrantLock
category: Java
tag:
  - Java Concurrency
---

> This article is reproduced from: <https://tech.meituan.com/2019/12/05/aqs-theory-and-apply.html>
>
> Author: Meituan Technical Team

Most synchronization classes in Java (such as Semaphore, ReentrantLock, etc.) are implemented based on AbstractQueuedSynchronizer (AQS). AQS is a simple framework that provides atomic management of synchronization state, blocking and waking threads, and a queue model.

This article will gradually delve from the application layer to the principle layer, and through the basic characteristics of ReentrantLock and its association with AQS, we will deeply interpret the knowledge points related to exclusive locks in AQS, while adopting a Q&A format to help everyone understand AQS. Due to space constraints, this article mainly elaborates on the logic of exclusive locks in AQS and the Sync Queue, without discussing the parts that include shared locks and Condition Queues (the core of this article is the analysis of AQS principles, and only a brief introduction to ReentrantLock is provided; interested readers can refer to the source code of ReentrantLock).

## 1 ReentrantLock

### 1.1 Overview of ReentrantLock Features

ReentrantLock means a reentrant lock, which refers to a thread being able to lock a critical resource repeatedly. To help everyone better understand the features of ReentrantLock, we will first compare ReentrantLock with the commonly used Synchronized, with the following features (the blue parts are the main points analyzed in this article):

![](https://p0.meituan.net/travelcube/412d294ff5535bbcddc0d979b2a339e6102264.png)

Below is a more intuitive comparison through pseudocode:

```java
// **************************Usage of Synchronized**************************
// 1. For code blocks
synchronized (this) {}
// 2. For objects
synchronized (object) {}
// 3. For methods
public synchronized void test () {}
// 4. Reentrant
for (int i = 0; i < 100; i++) {
  synchronized (this) {}
}
// **************************Usage of ReentrantLock**************************
public void test () throws Exception {
  // 1. Initialize to choose fair or unfair lock
  ReentrantLock lock = new ReentrantLock(true);
  // 2. Can be used for code blocks
  lock.lock();
  try {
    try {
      // 3. Supports various locking methods, more flexible; has reentrant characteristics
      if(lock.tryLock(100, TimeUnit.MILLISECONDS)){ }
    } finally {
      // 4. Manually release the lock
      lock.unlock();
    }
  } finally {
    lock.unlock();
  }
}
```

### 1.2 Association between ReentrantLock and AQS

From the above, we have learned that ReentrantLock supports both fair and unfair locks (for an analysis of the principles of fair and unfair locks, refer to “[Java Lock Matters You Must Know](https://mp.weixin.qq.com/s?__biz=MjM5NjQ5MTI5OA==&mid=2651749434&idx=3&sn=5ffa63ad47fe166f2f1a9f604ed10091&chksm=bd12a5778a652c61509d9e718ab086ff27ad8768586ea9b38c3dcf9e017a8e49bcae3df9bcc8&scene=38#wechat_redirect)”), and the underlying implementation of ReentrantLock is based on AQS. So how does ReentrantLock associate with AQS through fair and unfair locks? We will focus on understanding their relationship with AQS from the locking process (the association with AQS during the locking process is quite obvious, and the unlocking process will be introduced later).

The locking process in the unfair lock source code is as follows:

```java
// java.util.concurrent.locks.ReentrantLock#NonfairSync

// Unfair lock
static final class NonfairSync extends Sync {
  ...
  final void lock() {
    if (compareAndSetState(0, 1))
      setExclusiveOwnerThread(Thread.currentThread());
    else
      acquire(1);
    }
  ...
}
```

The meaning of this code is:

- If the CAS operation to set the State variable (synchronization state) is successful, meaning the lock is successfully acquired, then set the current thread as the exclusive owner thread.
- If the CAS operation to set the State variable (synchronization state) fails, meaning the lock acquisition failed, then enter the Acquire method for subsequent processing.

The first step is easy to understand, but what is the subsequent processing strategy after the lock acquisition fails? There may be the following
