# Chinese translation of Java8 Guide

As Java 8 became more popular, many people mentioned that Java 8 was also a very frequently asked point of knowledge in interviews. In response to your requests and needs, I intend to summarize this knowledge. I was going to summarize it myself, and then I saw GitHub with a related repository, address: [https://github.com/winterbe/java8-tutorian](https://github.com/winterbe/java8-tutorian). The repository is in English, and I have translated it and added and modified parts of it, which are set out below.

I don't...

Welcome to my introduction to Java 8. This curriculum will gradually guide you through all new language functions. On the basis of a short code example, you will learn how to use the default interface method, lambda expression, method reference, and repeatable annotations. At the end of this paper, you will be familiar with the latest API changes, such as streams, functional interfaces, extension of the Map class, and the new Date API. There is no big, boring text, just a bunch of comments.

## Default Method for Interfaces

Java 8 enables us to do this by adding non-abstract methods to the interface using the `default` keyword. This feature is also known as the [virtual extension method](http://stackoverflow.com/a/24102730).

First example:

```Java
public interface Formula {
    double calculate(int a);

    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}
```

The Formula interface defines the default method `sqrt` in addition to the abstract method `calculate`. The class that implements the interface requires only an abstract method `calculate`. The default method `sqrt` can be used directly. And of course, you can create objects directly through the interface, and then you can achieve the default method in the interface, and we can demonstrate it with the code.

```Java
public class Main {
    public static void main(String[] args) {
        // Access interfaces by anonymous inner class
        Formula formula = new Formula() {
            @Override
            public double calculate(int a) {
                return sqrt(a * 100);
            }
        };

        System.out.println(formula.calculate(100)); // 100.0
        System.out.println(formula.sqrt(16)); // 4.0
    }
}
```

Formula was done as an anonymous object. The code is very easy to understand, and line 6 achieves the calculation of `sqrt(a * 100)`. In the next section, we will see a better and easier way to achieve a single method in Java 8.

**Translator's note:** Both abstract classes and interfaces are accessible by anonymous inner classes. Objects cannot be created directly by abstract classes or interfaces. As for access to the interface above by means of an anonymous inner class, we can understand that an inner class implements the abstract method of the interface and returns an inner class object, which we then refer to by reference to the interface.

## Lambda Expression

First, look at how the strings are arranged in the old Java version:

```Java
List<String> names = Arrays.asList("Peter", "Anna", "Mike", "Xenia");

Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return b.compareTo(a);
    }
});
```

Only the static method `Collections.sort` is required to enter a List object and a comparator in the order specified. It is common practice to create an anonymous comparator object and then pass it to the `sort` method.

In Java 8, you don't have to use this traditional way of anonymous objects. Java 8 offers a simpler syntax, a lambda expression:

```Java
Collections.sort(names, (a, b) -> b.compareTo(a));
```

As can be seen, the code is shorter and more readable, but in practice, it can be even shorter:

```Java
names.sort((a, b) -> b.compareTo(a));
```

The List class itself has a `sort` method. And Java compilers can automatically infer the type of the parameter, so you can write it again. Now let's see what else is used in the lambda expression.

## Functional Interfaces

**Translator's Note:** The original text is not clear about this part of the explanation, so it's modified!

Java language designers put a lot of energy into thinking about how to make existing functions friendly for Lambda. The final approach was to add the concept of a functional interface. **A functional interface means an interface that contains only one abstract method but may have multiple non-abstract methods (i.e., the default method referred to above).** An interface like this can be converted to a lambda expression. `java.lang.Runnable` and `java.util.concurrent.Callable` are the two most typical examples of a functional interface. Java 8 adds a special annotation `@FunctionalInterface`, but this annotation is not usually required (in
