I don't...
title: Java 21 Overview of New Features (Important)
Category:
Tag:

- Java's New Character.
  I don't...

JDK 21 was released on 19 September 2023, a very important version, a milestone.

JDK 21 is a long-term support version of LTS, and so far there are four long-term support versions: JDK 8, JDK 11, JDK 17, and JDK 21.

JDK 21 has 15 new features, some of which are more important for detailed presentation in this article:

- [JEP 430: String Templates (Preview)](https://openjdk.org/jeps/430)
- [JEP 439: General ZGC (Intergenerational ZGC)](https://openjdk.org/jeps/439)
- [JEP 441: Pattern Matching for Switch](https://openjdk.org/jeps/441)
- [JEP 442: Foreign Function & Memory API (Third Preview)](https://openjdk.org/jeps/442)
- [JEP 443: Unnamed Patterns and Variables (Preview)](https://openjdk.org/jeps/443)
- [JEP 445: Unnamed Classes and Example Main Methods (Preview)](https://openjdk.org/jeps/445)

# JEP 430: String Template (Preview)

String Templates (string template) is still a preview feature for JDK 21.

String Templates provide a more concise and intuitive way to dynamically build strings. By using the placeholder `${}`, we can embed the value of the variable directly into a string without having to process it manually. When running, the Java compiler replaces these placeholders with the actual variable value. Also, expressions support local variables, static/non-static fields, or even methods, calculations, etc.

In fact, String Templates (string templates) are present in most programming languages:

```typescript
"Greetings {name}"; // Angular
`Greetings ${name}!`; // Typescript
"Greetings {name}"; // Visual Basic
"Greetings {name}"; // Python
```

Java Before the String Templates, we usually used string concatenation or formatting to construct a string:

```java
// Concatenation
String message = "Greetings " + name + "!";

// String.format()
String message = String.format("Greetings %s!", name); // concatenation

// MessageFormat
String message = new MessageFormat("Greetings!").format(name);

// StringBuilder
String message = new StringBuilder().append("Greetings ").append(name).toString();
```

These methods have more or less shortcomings, such as difficulty of reading, length, and complexity.

Java uses the String Templates for string aggregation, where expressions can be embedded directly in a string without additional processing:

```java
String message = STR."Greetings!";
```

In the template expression above:

- STR is the template processor.
- `${name}` is an expression that, while running, will be replaced by the corresponding variable value.

Java currently supports three template processors:

- STR: Automatically executes a string plug-in value, replacing each embedded expression in the template with its value (converted to a string).
- FMT: Similar to STR, but it can also accept format descriptors, which appear on the left side of the embedded expression to control the style of the output.
- RAW: The string template will not be automatically processed like the STR and FMT template processors, but returns a `StringTemplate` object, which contains text and expression information in the template.

```java
String name = "Lokeesh";

// STR
String message = STR."Greetings ${name}!";

// FMT
String message = FMT."Greetings %-12s.";

// RAW
String message = "Greetings.";
String message = STR.process(st);
```

In addition to the three template processors provided by JDK itself, you can implement the `StringTemplate.Processor` interface to create your own template processor, simply by inheriting the `StringTemplate.Processor` interface, and then implementing the `process` method.

We can use local variables, static/non-static fields, or even methods as embedded expressions:

```java
// Variable
String message = STR."Greetings!";

// Method
String message = STR."Greetings ${getName()}";

// Field
String message = "Greetings ${this.name}";
```

You can also perform calculations and print results in expressions:

```java
int x = 10, y = 20;
String s = STR."${y + x}"; // "10 + 20 = 30"
```

To improve readability, we can split embedded expressions into multiple lines:

```java
String message = STR."The current time is
// Sample company - document time in HH:mm:ss
DateTimeFormatter
.ofPattern("HH:mm:ss")
.format(LocalTime.now())";
```

# JEP 431: Sequenced Collection

JDK 21 introduced a new type of collection: \*\*Sequenced
