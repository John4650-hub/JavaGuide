# "Java 8 Guide" Chinese Translation

With the increasing popularity of Java 8, many people mention that Java 8 is a frequently asked topic in interviews. At the request and need of many, I plan to summarize this part of knowledge. Initially, I intended to summarize it myself, but later I found a related repository on GitHub at:
[https://github.com/winterbe/java8-tutorial](https://github.com/winterbe/java8-tutorial). This repository is in English, and I have translated it and added and modified some content. Below is the main text.

______________________________________________________________________

Welcome to my introduction to Java 8. This tutorial will guide you step by step through all the new language features. Based on brief code examples, you will learn how to use default interface methods, lambda expressions, method references, and repeatable annotations. By the end of this article, you will be familiar with the latest API changes, such as streams, functional interfaces, enhancements to the Map class, and the new Date API. There are no lengthy boring texts, only a bunch of commented code snippets.

## Default Methods for Interfaces

Java 8 allows us to add non-abstract method implementations to interfaces using the `default` keyword. This feature is also known as [virtual extension methods](http://stackoverflow.com/a/24102730).

The first example:

```java
interface Formula {

    double calculate(int a);

    default double sqrt(int a) {
        return Math.sqrt(a);
    }

}
```

In the Formula interface, in addition to the abstract method for calculating the formula, a default method `sqrt` is also defined. Classes implementing this interface only need to implement the abstract method `calculate`. The default method `sqrt` can be used directly. Of course, you can also create an object directly through the interface and implement the default method in the interface, let's demonstrate this with code.

```java
public class Main {

  public static void main(String[] args) {
    // Accessing the interface via an anonymous inner class
    Formula formula = new Formula() {
        @Override
        public double calculate(int a) {
            return sqrt(a * 100);
        }
    };

    System.out.println(formula.calculate(100));     // 100.0
    System.out.println(formula.sqrt(16));           // 4.0

  }

}
```

The formula is implemented as an anonymous object. This code is very easy to understand, achieving the calculation of `sqrt(a * 100)` in just 6 lines. In the next section, we will see a better and more convenient way to implement single method objects in Java 8.

**Translator's Note:** Both abstract classes and interfaces can be accessed via anonymous inner classes. Objects cannot be created directly from abstract classes or interfaces. For the above access to the interface via an anonymous inner class, we can understand it this way: an inner class implements the abstract method in the interface and returns an inner class object, and then we let the interface reference point to this object.

## Lambda Expressions

First, let's see how strings were sorted in older versions of Java:

```java
List<String> names = Arrays.asList("peter", "anna", "mike", "xenia");

Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return b.compareTo(a);
    }
});
```

You just need to pass a List object and a comparator to the static method `Collections.sort` to sort in the specified order. The usual approach is to create an anonymous comparator object and pass it to the `sort` method.

In Java 8, you no longer need to use this traditional anonymous object approach; Java 8 provides a more concise syntax, lambda expressions:

```java
Collections.sort(names, (String a, String b) -> {
    return b.compareTo(a);
});
```

As you can see, the code becomes shorter and more readable, but it can actually be written even shorter:

```java
Collections.sort(names, (String a, String b) -> b.compareTo(a));
```

For function bodies with only one line of code, you can omit the curly braces `{}` and the return keyword, but you can make it even shorter:

```java
names.sort((a, b) -> b.compareTo(a));
```

The List class itself has a `sort` method. Additionally, the Java compiler can automatically infer the parameter types, so you don't need to write the type again. Next, let's see what other uses lambda expressions have.

## Functional Interfaces

**Translator's Note:** The original text's explanation of this part was not very clear, so modifications were made!

The designers of the Java language invested a lot of effort in thinking about how to make existing functions friendly to support Lambda. The approach they ultimately took was to introduce the concept of functional interfaces. \*\*A "functional interface" is an interface that contains only one abstract method but can have multiple
