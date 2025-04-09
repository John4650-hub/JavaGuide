I don't...
Title: The principles and applications of AQS from ReentrantLock
Category:
Tag:

- Java.
  I don't...

> Reproduced from: <https://tech.meituan.com/2019/12/05/aqs-theory-and-apply.html>
>
> Author: Mission Technical Team

Most of Java's synchronized classes (Semaphore, ReentrantLock, etc.) are based on AbstractQueuedSynchronizer (known as AQS). AQS is a simple framework that provides atomic management synchronization, blocking and awakening threads, and queue models.

This paper will move from the application level to the principle level and will provide an in-depth reading of AQS-related monopolistic knowledge points through the basic features of ReentrantLock and the connection between ReentrantLock and AQS, as well as a question-and-answer model to help understand AQS. For reasons of space, this article focuses on the logic of the AQS lock and the Sync Queue, not on the section containing the shared lock and the Condition Queue (the core of this article is the AQS rationale profile, but simply describes ReentrantLock, the source code for which interested students can read).

# 1 ReentrantLock

Overview of ReentrantLock characteristics

ReentrantLock means re-lockable, meaning that a thread can re-lock a critical resource. In order to help you better understand the characteristics of ReentrantLock, we first compare ReentrantLock with the commonly used synchronized, as follows (the blue part is the point of the main profile of this article):

(https://p0.meituan.net/travelcube/412d2ff5535bcdc0d979b2a339e610264.png)

The following is a more intuitive comparison by means of pseudo code:

```Java
Use of synchronized**************
For code blocks
synchronized (this)
For objects
synchronized (object)
/ 3. Use in methodology
public synchronized void test()
Reentrant
for (int i = 0; i < 100; i++) {
synchronized (this)
♪ I'm sorry ♪
Use of ReentrantLock********************
public void test() throws Exception
/ / / / / / / / / / / / / / / / / / / / / / / / / /
ReentrantLock lock = new ReentrantLock(true);
// 2. Available for code blocks
lock.lock();
try
// 3. Support multiple locking methods, which are more flexible;
if (lock.tryLock())
{ 
} finally
// 4. Manual release lock
lock.unlock();
♪ I'm sorry ♪
} finally
lock.unlock();
♪ I'm sorry ♪
♪ I'm sorry ♪
```

## 1.2 ReentrantLock Link to AQS

As we already know above, ReentrantLock supports fair locks and non-fair locks (the rationale analysis of fair locks and non-fair locks can be found in the Java “Locks” (https://mp.weixin.qq.com/s?__biz=MjM5NjQ5MTI5OA=&mid=2651749434&idx=3&sn=5ffa63ad47fe166f1a9f9f6004ed10091&chksm=bd12a575778a652c6109d9e718ab086ff27ad876886ea9b38c3cf9e017a8e49b3df9c08& "Scene =38#wechat_redirect") and the bottom of ReentrantLock is achieved by AQS. So how does ReentrantLock relate to AQS through a fair and non-fair lock? We focus on understanding their relationship with AQS through the locking process (the link with AQS is more evident during the locking process and the subsequent presentation of the unlocking process).

The locking process in the non-fair lock source code is as follows:

```Java
// java.util.concurrent.locks.ReentrantLock#NonfairSync

// Unfair lock
// You can't have any of this.
final void lock() {
    if (compareAndSetState(0, 1)) {
        setExclusiveOwnerThread(Thread.currentThread());
    } else {
        acquire(1);
    }
}
```

This code means:

- Sets the current thread to a single thread if the variable state (Sync) is successful, i.e., the lock is successful.
- If the variable state (Sync state) fails by setting the CAS, i.e., the lock fails to be retrieved, then the acquire method is used for follow-up.

The first step is well understood, but what is the follow-up strategy after the second step to capture the lock failed? This piece may have the following thoughts:

- What's the follow-up to the failure of a linear lock? There are two possibilities:

(1) Sets the result of the current thread locking to fail and takes the locking process to end. Such a design would significantly reduce the confluence of systems and would not meet our actual needs. So the following process
