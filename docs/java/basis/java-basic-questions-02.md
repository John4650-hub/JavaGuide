I don't...
Title: Java Basic Common Interview Summary (C)
Category:
Tag:

- Java Foundation.
  Head:
- Meta.
- Name: keywords
  subject: object-oriented, process-oriented, OOP, POP, Java object, constructor method, encapsulation, inheritance, polymorphism, interface, abstract class, default method, static method, private method, deep copy, shallow copy, quoted copy, object category, equals, HashCode, ==, string, String, StringBuffer, StringBuilder, non-variability, string constant pool, intern, string spell, Java basic, interview question
- Meta.
- Name: description
  It is hoped that the most high-quality Java basic knowledge points and interview summaries available online will help you!
  I don't...

@include: @article-header.snippet.md--

# Object-oriented base

## Distinction between object- and process-oriented

The process-oriented programming (Procedural-Oriented Programming, POP) and object-oriented programming (Object-Oriented Programming, OOP) are two common programming paradigms, the main difference between which is that the problem is solved in different ways:

- **Process-oriented programming (POP)**: The process-oriented method of solving problems is broken down into a method, and problem-solving is implemented through a methodology.
- **Object-oriented Programming (OOP)**: Object-oriented programming will first abstract the object and then solve the problem in the way the object is executed.

Compared to POP, OOP development processes generally have the following advantages:

- **Easy to maintain**: OOP programs are usually easier to maintain due to good structure and encapsulation.
- **Reusable**: OOP design makes the code more reusable and expands its functionality through inheritance and polymorphism.
- **Easy to expand**: Modularized design makes system expansion easier and more flexible.

POPs are usually programmed in a simpler and more direct manner and are suitable for simple tasks.

The difference in performance between POP and OOP depends primarily on their operating mechanisms and not just on the programming paradigm itself. So a simple comparison of the performances of the two is a common error (relevant issue): [Process-oriented: process-oriented performance is higher than object-oriented?](https://github.com/Snailclimb/JavaGuide/issues/431).

(https://oss.javaguide.cn/github/javaguide/java/basis/pop-vs-oop-performance.png)

Performance is not the only consideration when choosing a programming paradigm. The maintenance, scalability, and development efficiency of code are equally important.

Modern programming languages basically support a variety of programming paradigms, both process-oriented and object-oriented.

The following is an example of a rounded area and a perimeter, with a simple presentation of two different, object- and process-oriented solutions.

**Object-oriented**:

```Java
This post is part of our special coverage Syria Protests 2011.
// Define Radius of Circle
double radius;

// Construct Functions
public Circle(double radius) {
    this.radius = radius;
}

// Size of calculated circle
public double getArea() {
    return Math.PI * radius * radius;
}

// The perimeter of the calculation circle
public double getPerimeter() {
    return 2 * Math.PI * radius;
}

public static void main(String[] args) {
    // Create a circle with a radius of 3
    Circle circle = new Circle(3.0);

    // Size and perimeter of output circle
    System.out.println("Circle area: " + circle.getArea());
    System.out.println("The circle's perimeter is: " + circle.getPerimeter());
}
```

We have defined a `Circle` class for the circle, which includes radius properties of the circle and methods of calculating area and perimeter.

**Process-oriented**:

```Java
public class Main {
    public static void main(String[] args) {
        // Define Radius of Circle
        double radius = 3.0;

        // Calculate the area and perimeter of the circle
        double area = Math.PI * radius * radius;
        double perimeter = 2 * Math.PI * radius;

        // Size and perimeter of output circle
        System.out.println("Area of Circle: " + area);
        System.out.println("The perimeter of the circle is: " + perimeter);
    }
}
```

We define the radius of the circle and use it to calculate the area and perimeter of the circle directly.

What kind of operator does an object use? What's the difference between object entities and object references?

New operators, new examples of the creation of objects (examples of objects in stack memory), examples of object references to objects (object references in stack memory).

- An object reference can point to 0 or 1 object (a rope can be attached to a balloon or a balloon);
- An object can have n references pointing at it (a balloon can be attached to n ropes).

# Equality of object and equality difference of reference

- The general comparison of objects is whether the contents stored in the memory are equal.
- The reference to an equivalent general comparison is whether the memory addresses to
