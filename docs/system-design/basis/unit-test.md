I don't...
Title: What is a unit test? What should we do?
Category: Code quality
I don't...

> This paper has been restructured from the article [on why to write unit tests - Keyboard Men - 2016](https://www.jianshu.com/p/fa41fb80d2b8).

# What's a unit test?

This is how Wikipedia introduces the unit test:

> In computer programming, unit testing (Unit Test) is the correctness test for the program module (the smallest unit of software design).
>
> A program module is applied as a **Minimum testable component**. In process programming, one unit is a single program, function, process, etc.; for object-oriented programming, the smallest unit is the method, including the method in the base category (super-category), abstract category, or derivative category (subcategory).

Because of the independent logic of each module, in conducting the module tests, we often use Fake, Stub, and Mock to isolate external dependencies and ensure that they do not affect the validation logic.

With regard to the interpretation of the concepts Fake, Mock, and Stub, it is possible to look at the article: [Fakes, Mocks, and Stubs in the test] Clarity of the concept - the King invites the Moon Bear - 2018\](https://zhuanlan.zhihu.com/p/26942686).

Why do you need a unit test?

# To re-construct the escort

As I wrote in [reconstructing](./refactoring.md):

The module test can provide confidence in the reprogramming and reduce the reprogramming costs. We need to focus on unit tests as much as we do on production code.

Every developer goes through re-engineering, and it's not uncommon to change the code after that re-engineering. It's likely that you just modified a simple way to make a more serious error in the system.

If there was a unit test, there would be no risk. One category is completed, and the unit test is written to ensure that it is logical; the second category, the unit test... 100 classes are written, and, as is the case, each category does the first point of “ensures the logical correctness,” and there is no problem in aggregating 100 categories. You can easily re-engineer and run the APP while you're at it; instead of re-engineering as a whole, you can run.

Improved code quality

Since each module has independent logic, unit testing needs to be conducted in isolation from external dependencies to ensure that they do not affect the validation logic. Because of the separation of various dependencies, module testing will facilitate the separation of components in the project, the organization of project dependencies, and a greater reduction in code alignment. The code so written is better maintained and expanded, thus improving the quality of the code.

# Reduce bugs

A machine consisting of a variety of small parts, if one of them is broken, the machine runs out. It is essential to ensure that each component is in accordance with the specifications required by the design plan in order for the machine to function properly.

A unit-tested project will divide operations and functions into smaller, stand-alone logical components called units. The objective of module testing is to ensure the logical validity of the modules. The unit test guarantees that the “parts” of the project are performed in accordance with the “specifications” (demands), thus ensuring that the entire “machine” (project) works correctly and minimizes bugs.

# Quick bug positioning

If the program has bugs, we run a full unit test, find a non-passed test, and we can quickly locate the corresponding code. After repairing the code, run the corresponding unit test; if it does not pass, continue to modify and run the test... until **the test passes**.

# Continuous Integration and Unit Testing

Continuous integration relies on unit tests, which automatically run the unit tests to detect code errors when the continuous integration service automatically builds new code.

Who forces you to write the module test?

## Leadership requirements

Some experienced leaders, to a greater or lesser extent, would require a team to write module tests. It makes sense for a teammate who has some work experience; for a graduate who has no experience, I'm afraid they'll struggle.

The testing of a new person's module is a difficult task. The new person's code style has not been developed, nor do they know how important unit testing is, and mandatory unit testing would confuse them and prevent them from writing their own code.

# All developers write unit tests

Many of the home-based open-source projects abroad have a large number of unit tests. For example, [retrofit](https://link.jianshu.com?t=https://github.com/square/retrofit/tree/master/retrofit/src/test/java/retrofit2), [okhttp](https://link.jianshu.com?t=https://github.com/square/okhttp/tree/master/okhttp-test/src/java/okhttp3), \[butterknife\](https://link.jianshu
