I don't...
title: Java 20 Overview of New Features
Category:
Tag:

- Java's new character.
  I don't...

JDK 20 was released on 21 March 2023, as a non-permanently supported version.

According to the development plan, the next LTS version is JDK 21, to be released in September 2023.

![Java 20 Overview](https://oss.javaguide.cn/github/javaguide/java/new-features/640.png)

JDK 20 has only seven new features:

- [JEP 429: Scoped Values (Rural Values)](https://openjdk.org/jeps/429) (first incubation)
- [JEP 432: Record Patterns (record mode)](https://openjdk.org/jeps/432) (second preview)
- [JEP 433: Switch Mode Match](https://openjdk.org/jeps/433) (fourth preview)
- [JEP 434: Foreign Function & Memory API](https://openjdk.org/jeps/434) (second preview)
- [JEP 436: Virtual Threads (virtual threads)](https://openjdk.org/jeps/436) (second preview)
- [JEP 437: Structured Concurrency (structured and distributed)](https://openjdk.org/jeps/437) (second incubation)
- [JEP 438: Vector API](https://openjdk.org/jeps/438) (fifth incubation)

# JEP 429: Domain Value (first hatching)

The Scoped Values allow for the sharing of immutable data within and across the online range, which is better than linear local variables, especially when a large number of virtual threads are used.

```java
final ScopedValue<V> scopedValue = ...;

In some method
ScopedValue<V> where (V, <value>)
.run(() -> { V.get(); /* Call methods */ });
```

In a method called directly or indirectly from the lambda expression:

```java
...V.get()...
```

The domain value allows for the safe and effective sharing of data between components in large programs without recourse to method parameters.

For a detailed description of the domain value, it is recommended to read [Assisting Question Answer for Role Domain Value](https://www.happycoders.eu/java/scoped-values/).

# JEP 432: Record Mode (second preview)

Record mode (Record Patterns) can deconstruct the value of a record, i.e., more easily extract data from the Record Class. Furthermore, it can be combined with embedded record-keeping and type models to achieve strong, declarative, and groupable forms of data navigation and processing.

The record mode cannot be used alone but is used in conjunction with the instanceof or switch mode.

Let's start with a simple demonstration of the situation.

A simple definition of a record class:

```java
record Shape(String type, long unit) {}
```

Before record mode:

```java
Shape shape = new Shape("Circle", 10);
if (circle instanceof Shape) {
    System.out.println("Area of " + shape.type() + " is " + Math.PI * Math.pow(shape.unit(), 2));
}
```

After record mode:

```java
Shape shape = new Shape("Circle", 10);
if (circle instanceof Shape) {
    System.out.println("Area of " + type + " is: " + Math.PI * Math.pow(unit, 2));
}
```

Look at how the record mode works with the switch.

Define some categories:

```java
interface Shape {}
record Circle(double radius) implements Shape {}
record Square(double side) implements Shape {}
record Rectangle(double length, double width) implements Shape {}
```

Before record mode:

```java
Shape shape = new Circle(10);
switch (shape) {
    case Circle c:
        System.out.println("The shape is Circle with area: " + Math.PI * c.radius() * c.radius());
        break;
    case Square s:
        System.out.println("The shape is Square with area: " + s.side() * s.side());
        break;
    case Rectangle r:
        System.out.println("The shape is Rectangle with area: " + r.length() * r.width());
        break;
    default:
        System.out.println("Unknown Shape");
}
```

After record mode:

```java
Shape shape = new Circle(10);
switch (shape) {
    case Circle(double radius):
        System.out.println("The shape is Circle with area: " + Math.PI * radius * radius);
        break;
    case Square(double side):
        System.out.println("The shape is Square with area: " + side * side);
        break;
    case Rectangle(double length, double width):
        System.out.println("The shape is Rectangle with area: " + length * width);
        break;
    default:
        System.out.println("Unknown Shape");
}
```

Recording patterns avoid unnecessary conversions and make code simpler and more readable
