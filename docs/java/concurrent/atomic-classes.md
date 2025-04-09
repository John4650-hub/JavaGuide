I don't...
Title: Atomic Atomic Summary
Category:
Tag:

- Java.
  I don't...

# Atomic Atomic Introduction

`Atomic` is translated into Chinese as meaning “atoms”. Chemically, atoms are the smallest units that make up substances and are inseparable from chemical reactions. In programming, `Atomic` means that an operation is atomic, that is, it is indivisible and non-interruptible. Even when multiple threads are executed simultaneously, the operation is either fully implemented or not implemented, and will not be partially completed by other threads.

The atomic class is simply a class with atomic operational characteristics.

The `Atomic` atoms in the `java.util.concurrent.atomic` package provide a linearly safe way of operating individual variables.

The `Atomic` category relies on the CAS (Compare-And-Swap, compare and trade) optimistic lock to ensure the atomicity of its methods without the need to use traditional locking mechanisms (e.g. `synchronized` blocks or `ReentrantLock`).

This article, which only introduces the Atomic Atoms concept, is a practical realization that can be read by the author: [CAS](.../cas.md).

\[JUC atoms overview\](https://oss.javaguide.cn/github/javaguide/java/JUC%E5%85%83%E7%B1%BB%E6%A6%A2%E8%BF%B0%E5%9B%9B%E5%8D%81%E5%9B%9B%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8D%81%E5%8
