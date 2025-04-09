I don't...
Title: Java Syntax
Category:
Tag:

- Java Foundation
  Head:
- Meta
- Name: keywords
  Contact: Java Grammar
- Meta
- Name: description
  Content: This article describes 12 types of grammatical sugar commonly used in Java. The so-called glucose is just a grammar that is available to developers for easy development. However, this grammar is known only by developers. In order to be implemented, sugar is required, i.e., to convert to a syntax known as JVM. And when we decompose the grammar, you find that these convenient syntaxes that we use on a daily basis are actually made up of other, simpler syntaxes. With these grammatical sugars, we can significantly increase efficiency in day-to-day development, but also avoid overuse. It would be preferable to understand the rationale before it is used and to avoid a fallout.
  I don't...

> Author: Hollis

Original language: <https://mp.weixin.qq.com/s/o4XdEMq1DL-nBS-f8Za5Aw>

Grammar sugar is one of the points of knowledge often asked in big factory Java interviews.

From the perspective of Java's coding principles, deep bytes, and class files, stripping, understanding the grammar sugar in Java and its use helps you to understand the principles behind these syntax sugars while learning how to use Java.

# What's glucose?

**Syntax Sugar**, also known as sugar, is a term invented by British computer scientist Peter J. Landin to refer to a grammatical addition to the computer language, which does not affect the function of the language but is more user-friendly for programmers. In short, grammar sugar makes the process simpler and more readable.

![Image](https://oss.javaguide.cn/github/javaguide/java/basis/syntactic-sugar/image20208181775953954.png)

Interestingly, in the programming area, there is no extension to the term grammatical salt and glucose.

We know almost all of our programming languages with grammatical sugar. According to the author, the amount of grammatical sugar is one of the criteria for judging whether a language is strong enough. Many people say that Java is a “low sugar language” and that Java has been adding sugar at the level of Java 7 since then, mainly under Project Coin. Although now Java, some people still think that Java is low sugar, and it's going to continue in the direction of high sugar.

# What are the common grammar sugars in Java?

As mentioned above, the presence of grammatical sugar is mainly for the convenience of developers. But actually, **Java Virtual Machine does not support these syntax sugars. These grammatical sugars are restored to a simple basic grammatical structure at the compilation stage, which is the process of grammatical sugar.**

As far as codification is concerned, it must have been known that in the Java language, the `javac` order could have been translated into a byte code for the `.class` that could be operated on the Java Virtual Machine. If you look at the source code of `com.sun.tools.javac.main.Javacompiler`, you will find that one of the steps in `compile()` is to call `desugar()`, a method that is responsible for the solution of grammar sugar.

The most commonly used grammatical sugar in Java consists mainly of broad, long parameters, compilation of conditions, self-dismantling boxes, internal categories, etc. This is mainly an analysis of the principles behind these syntax sugars. One step at a time, strip off the sugar coat and look at its nature.

We will use [reciprocal](https://mp.weixin.qq.com/s?biz=Mzi3NzE0NjcwMg=265206009&idx=1&sn=565963639763d55b48cee78&chksm=f36bc80c41c3596a1b9501c62801f1f9e06d07af354474e36f36f06f016df66773a7ba&scene=21#wechat_redirect) for online reverse translations of Class documents.

## Switch support for String and Enumeration

As mentioned earlier, grammatical sugar in the Java language has gradually enriched since Java 7, and one of the more important things is that `switch` in Java 7 began to support `String`.

Before the start, `switch` in Java itself supported the basic types: `int`, `char`, etc. For the `int` type, a direct comparison of values is made. The `char` type is compared to its ASCII code. Thus, for compilers, only integers are actually used in `switch`, and any type of comparison needs to be converted into one, like `byte`, `short`, `char` (ASCII code is integer), and `int`.

Then look at `switch` support for `String`, with the following codes:

```Java
public class SwitchDemoString {
    public static void main(String[] args) {
        String str = "world";
        switch (str) {
            case "hello":
```
