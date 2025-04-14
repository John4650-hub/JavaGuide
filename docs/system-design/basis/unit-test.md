---
title: What Exactly is Unit Testing? How Should It Be Done?
category: Code Quality
---

> This article is a refined version of [Talking About Why to Write Unit Tests - Keyboard Guy - 2016](https://www.jianshu.com/p/fa41fb80d2b8).

## What is Unit Testing?

Wikipedia describes unit testing as follows:

> In computer programming, unit testing is a correctness verification testing work conducted on program modules (the smallest unit of software design).
>
> A program unit is the **smallest testable part** of an application. In procedural programming, a unit is a single program, function, procedure, etc.; for object-oriented programming, the smallest unit is a method, including methods in base classes (superclasses), abstract classes, or derived classes (subclasses).

Since each unit has independent logic, during unit testing, we often use Fake, Stub, and Mock to isolate external dependencies and ensure that these dependencies do not affect the verification logic.

For interpretations of the concepts of Fake, Mock, and Stub, you can refer to this article: [Clarifying the Concepts of Fakes, Mocks, and Stubs in Testing - Wang Xia Yao Yue Xiong - 2018](https://zhuanlan.zhihu.com/p/26942686).

## Why is Unit Testing Necessary?

### Safeguarding Refactoring

In my article on [Refactoring](./refactoring.md), I wrote:

> Unit tests can provide confidence for refactoring and reduce the cost of refactoring. We should value unit tests as much as we value production code.

Every developer goes through refactoring, and it is not uncommon to end up breaking the code after refactoring. It is very likely that a simple modification to a method could lead to a serious error in the system.

With unit tests in place, this risk is eliminated. After writing a class, you write unit tests to ensure the logic of that class is correct; then you write the second class, unit tests... and so on for 100 classes. The principle is the same: ensuring "logical correctness" for each class means that when 100 classes are combined, there will be no issues. You can confidently refactor while running the APP, rather than being anxious about running it after a complete refactor.

### Improving Code Quality

Since each unit has independent logic, unit testing requires isolating external dependencies to ensure that these dependencies do not affect the verification logic. By separating various dependencies, unit testing promotes component decomposition in the project, organizes project dependencies, and reduces code coupling to a greater extent. The code produced this way is easier to maintain and extend, thereby improving code quality.

### Reducing Bugs

A machine is composed of various small parts, and if one part fails, the machine malfunctions. Each part must meet the specifications required by the design for the machine to operate correctly.

A unit-testable project divides business and functionality into smaller, logically independent components called units. The goal of unit testing is to ensure the logical correctness of each unit. Unit tests ensure that each "part" of the project executes according to "specifications" (requirements), thereby ensuring that the entire "machine" (project) runs correctly and minimizing bugs.

### Quickly Locating Bugs

If there is a bug in the program, we can run all unit tests and quickly locate the corresponding execution code by identifying the failing tests. After fixing the code, we run the corresponding unit tests; if they still fail, we continue to modify and run tests... until **the tests pass**.

### Continuous Integration Relies on Unit Testing

Continuous integration relies on unit testing. After the continuous integration service automatically builds new code, it will automatically run unit tests to discover code errors.

## Who Forces You to Write Unit Tests?

### Leadership Requirements

Some experienced leaders will require their teams to write unit tests to some extent. This requirement is quite reasonable for teammates with some work experience; however, for those with less experience or fresh graduates, it can be overwhelming. If they can't even write code properly, how can they be expected to write unit tests? Are you kidding me?

Training newcomers on how to use unit tests is a daunting task. Newcomers have not yet formed their coding style and may not understand the importance of unit tests. Forcing unit tests on them can lead to confusion and hinder their ability to write code according to their own thought processes.

### Influential Figures Write Unit Tests

Many well-known open-source projects abroad have extensive unit tests. For example, [retrofit](https://link.jianshu.com?t=https://github.com/square/retrofit/tree/master/retrofit/src/test/java/retrofit2), [okhttp](https://link.jianshu.com?t=https://github.com/square/okhttp/tree/master/okhttp-tests/src/test/java/okhttp3), \[butterknife\](https://link.jianshu.com?t=https://github.com/JakeWharton/butterknife/tree/master/butterknife-compiler/src/test/java/b
