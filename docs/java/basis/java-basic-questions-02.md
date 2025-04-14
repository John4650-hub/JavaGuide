---
title: Summary of Common Java Interview Questions (Part 2)
category: Java
tag:
  - Java Basics
head:
  -   - meta
      - name: keywords
        content: Object-Oriented, Procedural, OOP, POP, Java Objects, Constructor, Encapsulation, Inheritance, Polymorphism, Interface, Abstract Class, Default Method, Static Method, Private Method, Deep Copy, Shallow Copy, Reference Copy, Object Class, equals, hashCode, ==, String, String, StringBuffer, StringBuilder, Immutability, String Constant Pool, intern, String Concatenation, Java Basics, Interview Questions
  -   - meta
      - name: description
        content: A summary of the highest quality common knowledge points and interview questions about Java basics on the internet, hoping to be helpful to you!
---

<!-- @include: @article-header.snippet.md -->

## Basics of Object-Oriented Programming

### Differences Between Object-Oriented and Procedural Programming

Procedural-Oriented Programming (POP) and Object-Oriented Programming (OOP) are two common programming paradigms, and their main difference lies in the way they solve problems:

- **Procedural-Oriented Programming (POP)**: POP breaks down the process of solving a problem into individual methods, solving the problem through the execution of these methods.
- **Object-Oriented Programming (OOP)**: OOP first abstracts objects and then solves problems by executing methods on these objects.

Compared to POP, programs developed using OOP generally have the following advantages:

- **Ease of Maintenance**: Due to good structure and encapsulation, OOP programs are usually easier to maintain.
- **Ease of Reuse**: Through inheritance and polymorphism, OOP design makes code more reusable and facilitates functional expansion.
- **Ease of Extension**: Modular design makes system expansion easier and more flexible.

The programming style of POP is usually simpler and more direct, suitable for handling simpler tasks.

The performance difference between POP and OOP mainly depends on their execution mechanisms, not just the programming paradigms themselves. Therefore, simply comparing the performance of the two is a common misconception (related issue: [Is procedural performance higher than object-oriented?](https://github.com/Snailclimb/JavaGuide/issues/431)).

![Inappropriate performance comparison between POP and OOP](https://oss.javaguide.cn/github/javaguide/java/basis/pop-vs-oop-performance.png)

When choosing a programming paradigm, performance is not the only consideration. Code maintainability, scalability, and development efficiency are equally important.

Modern programming languages generally support multiple programming paradigms, allowing for both procedural and object-oriented programming.

Here is an example of calculating the area and circumference of a circle, demonstrating both object-oriented and procedural solutions.

**Object-Oriented**:

```java
public class Circle {
    // Define the radius of the circle
    private double radius;

    // Constructor
    public Circle(double radius) {
        this.radius = radius;
    }

    // Calculate the area of the circle
    public double getArea() {
        return Math.PI * radius * radius;
    }

    // Calculate the circumference of the circle
    public double getPerimeter() {
        return 2 * Math.PI * radius;
    }

    public static void main(String[] args) {
        // Create a circle with a radius of 3
        Circle circle = new Circle(3.0);

        // Output the area and circumference of the circle
        System.out.println("The area of the circle is: " + circle.getArea());
        System.out.println("The circumference of the circle is: " + circle.getPerimeter());
    }
}
```

We defined a `Circle` class to represent a circle, which includes the radius property and methods to calculate the area and circumference.

**Procedural**:

```java
public class Main {
    public static void main(String[] args) {
        // Define the radius of the circle
        double radius = 3.0;

        // Calculate the area and circumference of the circle
        double area = Math.PI * radius * radius;
        double perimeter = 2 * Math.PI * radius;

        // Output the area and circumference of the circle
        System.out.println("The area of the circle is: " + area);
        System.out.println("The circumference of the circle is: " + perimeter);
    }
}
```

We directly defined the radius of the circle and used it to calculate the area and circumference.

### What operator is used to create an object? What is the difference between an object entity and an object reference?

The `new` operator is used to create an object instance (the object instance is in heap memory), and the object reference points to the object instance (the object reference is stored in stack memory).

- An object reference can point to 0 or 1 object (like a string can either not tie a balloon or tie one balloon);
- An object can have n references pointing to it
