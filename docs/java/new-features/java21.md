---
title: Overview of New Features in Java 21 (Important)
category: Java
tag:
  - New Features in Java
---

JDK 21 was released on September 19, 2023, and it is a very significant version, a milestone.

JDK 21 is an LTS (Long-Term Support version), making it the fourth long-term support version alongside JDK 8, JDK 11, and JDK 17.

JDK 21 includes a total of 15 new features, and this article will detail some of the more important ones:

- [JEP 430: String Templates (Preview)](https://openjdk.org/jeps/430)
- [JEP 431: Sequenced Collections](https://openjdk.org/jeps/431)
- [JEP 439: Generational ZGC](https://openjdk.org/jeps/439)
- [JEP 440: Record Patterns](https://openjdk.org/jeps/440)
- [JEP 441: Pattern Matching for switch](https://openjdk.org/jeps/442)
- [JEP 442: Foreign Function & Memory API (Third Preview)](https://openjdk.org/jeps/442)
- [JEP 443: Unnamed Patterns and Variables (Preview)](https://openjdk.org/jeps/443)
- [JEP 444: Virtual Threads](https://openjdk.org/jeps/444)
- [JEP 445: Unnamed Classes and Instance Main Methods (Preview)](https://openjdk.org/jeps/445)

## JEP 430: String Templates (Preview)

String Templates are still a preview feature in JDK 21.

String Templates provide a more concise and intuitive way to dynamically construct strings. By using placeholders `${}`, we can directly embed variable values into the string without manual handling. At runtime, the Java compiler will replace these placeholders with the actual variable values. Moreover, expressions can include local variables, static/non-static fields, methods, and calculated results.

In fact, String Templates exist in most programming languages:

```typescript
"Greetings {{ name }}!";  // Angular
`Greetings ${ name }!`;    // TypeScript
$"Greetings { name }!"    // Visual Basic
f"Greetings { name }!"    // Python
```

Before the introduction of String Templates in Java, we typically used string concatenation or formatting methods to construct strings:

```java
// concatenation
message = "Greetings " + name + "!";

// String.format()
message = String.format("Greetings %s!", name);  // concatenation

// MessageFormat
message = new MessageFormat("Greetings {0}!").format(name);

// StringBuilder
message = new StringBuilder().append("Greetings ").append(name).append("!").toString();
```

These methods have drawbacks, such as being hard to read or overly verbose and complex.

With String Templates, string concatenation can directly embed expressions within the strings without additional processing:

```java
String message = STR."Greetings \{name}!";
```

In the above template expression:

- STR is the template processor.
- `\{name}` is the expression, which will be replaced with the corresponding variable value at runtime.

Java currently supports three types of template processors:

- STR: Automatically performs string interpolation, replacing each embedded expression in the template with its value (converted to a string).
- FMT: Similar to STR, but it also accepts format specifiers that appear to the left of the embedded expressions to control the output style.
- RAW: Unlike STR and FMT, this template processor does not automatically process string templates but returns a `StringTemplate` object containing the text and expression information from the template.

```java
String name = "Lokesh";

// STR
String message = STR."Greetings \{name}.";

// FMT
String message = FMT."Greetings %-12s\{name}.";

// RAW
StringTemplate st = RAW."Greetings \{name}.";
String message = STR.process(st);
```

In addition to the three built-in template processors, you can also implement the `StringTemplate.Processor` interface to create your own template processor by extending the `StringTemplate.Processor` interface and implementing the `process` method.

We can use local variables, static/non-static fields, and even methods as embedded expressions:

```java
// variable
message = STR."Greetings \{name}!";

// method
message = STR."Greetings \{getName()}!";

// field
message = STR."Greetings \{this.name}!";
```

Calculations can also be executed within expressions and printed as results:

```java
int x = 10, y = 20;
String s = STR."\{x} + \{y} = \{x + y}";  //"10 + 20 = 30"
```

For better readability, we can split embedded expressions into multiple lines:

```java
String time = STR."The current time is \{
    // sample comment - current time in HH:mm:ss
    DateTimeFormatter
      .ofPattern("HH:mm:ss")
      .format(LocalTime.now())
  }.";
```

## JEP 431: Sequenced Collections

JDK 21 introduces a new collection type: **Sequenced Collections** (also known as ordered collections), which are collections that have a determined encounter order (regardless of how many times we iterate through this collection, the order of elements remains fixed). Sequenced Collections provide simple methods for accessing, adding, or removing the first and last elements and for obtaining reverse views (the opposite order of the original collection).

Sequenced Collections consist of the following three interfaces:

- [`SequencedCollection`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/SequencedCollection.html)
- [`SequencedSet`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/SequencedSet.html)
- [`SequencedMap`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/SequencedMap.html)

The `SequencedCollection` interface extends the `Collection` interface, offering methods for accessing, adding, or removing elements at either end of the collection, as well as methods for obtaining a reverse view of the collection.

```java
interface SequencedCollection<E> extends Collection<E> {

  // New Method

  SequencedCollection<E> reversed();

  // Promoted methods from Deque<E>

  void addFirst(E);
  void addLast(E);

  E getFirst();
  E getLast();

  E removeFirst();
  E removeLast();
}
```

The `List` and `Deque` interfaces implement the `SequencedCollection` interface.

We can demonstrate its practical usage with `ArrayList` as an example:

```java
ArrayList<Integer> arrayList = new ArrayList<>();

arrayList.add(1);   // List contains: [1]

arrayList.addFirst(0);  // List contains: [0, 1]
arrayList.addLast(2);   // List contains: [0, 1, 2]

Integer firstElement = arrayList.getFirst();  // 0
Integer lastElement = arrayList.getLast();  // 2

List<Integer> reversed = arrayList.reversed();
System.out.println(reversed); // Prints [2, 1, 0]
```

The `SequencedSet` interface directly extends the `SequencedCollection` interface and overrides the `reversed()` method.

```java
interface SequencedSet<E> extends SequencedCollection<E>, Set<E> {

    SequencedSet<E> reversed();
}
```

The `SortedSet` and `LinkedHashSet` interfaces implement the `SequencedSet` interface.

We can show its practical usage with `LinkedHashSet` as an example:

```java
LinkedHashSet<Integer> linkedHashSet = new LinkedHashSet<>(List.of(1, 2, 3));

Integer firstElement = linkedHashSet.getFirst();   // 1
Integer lastElement = linkedHashSet.getLast();    // 3

linkedHashSet.addFirst(0);  // List contains: [0, 1, 2, 3]
linkedHashSet.addLast(4);   // List contains: [0, 1, 2, 3, 4]

System.out.println(linkedHashSet.reversed());   // Prints [4, 3, 2, 1, 0]
```

The `SequencedMap` interface extends the `Map` interface, offering methods for accessing, adding, or removing key-value pairs at either end, obtaining a `SequencedSet` of keys, a `SequencedCollection` of values, a `SequencedSet` of entries (key-value pairs), and a reverse view of the collection.

```java
interface SequencedMap<K,V> extends Map<K,V> {

  // New Methods

  SequencedMap<K,V> reversed();

  SequencedSet<K> sequencedKeySet();
  SequencedCollection<V> sequencedValues();
  SequencedSet<Entry<K,V>> sequencedEntrySet();

  V putFirst(K, V);
  V putLast(K, V);

  // Promoted Methods from NavigableMap<K, V>

  Entry<K, V> firstEntry();
  Entry<K, V> lastEntry();

  Entry<K, V> pollFirstEntry();
  Entry<K, V> pollLastEntry();
}
```

The `SortedMap` and `LinkedHashMap` interfaces implement the `SequencedMap` interface.

We can demonstrate its practical usage with `LinkedHashMap` as an example:

```java
LinkedHashMap<Integer, String> map = new LinkedHashMap<>();

map.put(1, "One");
map.put(2, "Two");
map.put(3, "Three");

map.firstEntry();   // 1=One
map.lastEntry();    // 3=Three

System.out.println(map);  // {1=One, 2=Two, 3=Three}

Map.Entry<Integer, String> first = map.pollFirstEntry();   // 1=One
Map.Entry<Integer, String> last = map.pollLastEntry();    // 3=Three

System.out.println(map);  // {2=Two}

map.putFirst(1, "One");     // {1=One, 2=Two}
map.putLast(3, "Three");    // {1=One, 2=Two, 3=Three}

System.out.println(map);  // {1=One, 2=Two, 3=Three}
System.out.println(map.reversed());   // {3=Three, 2=Two, 1=One}
```

## JEP 439: Generational ZGC

JDK 21 extends the functionality of ZGC, adding support for generational garbage collection. However, it is turned off by default and needs to be enabled through configuration:

```bash
// Enable Generational ZGC
java -XX:+UseZGC -XX:+ZGenerational ...
```

In future releases, the official intention is to make `ZGenerational` the default, which means that generational ZGC will be enabled by default. In a later version, non-generational ZGC will be removed.

> In a future release we intend to make Generational ZGC the default, at which point -XX:-ZGenerational will select non-generational ZGC. In an even later release we intend to remove non-generational ZGC, at which point the ZGenerational option will become obsolete.
>
> 在将来的版本中，我们打算将 Generational ZGC 作为默认选项，此时 -XX:-ZGenerational 将选择非分代 ZGC。在更晚的版本中，我们打算移除非分代 ZGC，此时 ZGenerational 选项将变得过时。

Generational ZGC can significantly reduce pause times during garbage collection and improve application responsiveness. This is particularly valuable for large Java applications and high-concurrency performance optimizations.

## JEP 440: Record Patterns

Record Patterns were first previewed in Java 19, proposed by [JEP 405](https://openjdk.org/jeps/405). The second preview occurred in JDK 20, proposed by [JEP 432](https://openjdk.org/jeps/432). Finally, Record Patterns were officially added in JDK 21.

[Overview of New Features in Java 20](./java20.md) has detailed the Record Patterns, so we won't repeat them here.

## JEP 441: Pattern Matching for switch

This enhances switch expressions and statements in Java, allowing the use of patterns in case labels. When patterns match, the code corresponding to the respective case label is executed.

In the code below, the switch expression uses type patterns for matching.

```java
static String formatterPatternSwitch(Object obj) {
    return switch (obj) {
        case Integer i -> String.format("int %d", i);
        case Long l    -> String.format("long %d", l);
        case Double d  -> String.format("double %f", d);
        case String s  -> String.format("String %s", s);
        default        -> obj.toString();
    };
}
```

## JEP 442: Foreign Function and Memory API (Third Preview)

Java programs can interoperate with code and data outside the Java runtime through this API. By efficiently calling external functions (code outside the JVM) and securely accessing external memory (memory not managed by the JVM), this API enables Java programs to call native libraries and handle native data without the danger and fragility associated with JNI.

The Foreign Function and Memory API underwent its first incubation in Java 17, proposed by [JEP 412](https://openjdk.java.net/jeps/412). It had its second incubation in Java 18, proposed by [JEP 419](https://openjdk.org/jeps/419). Java 19 saw its first preview, proposed by [JEP 424](https://openjdk.org/jeps/424). JDK 20 had its second preview, proposed by [JEP 434](https://openjdk.org/jeps/434). JDK 21 presents its third preview, proposed by [JEP 442](https://openjdk.java.net/jeps/442).

I have detailed the Foreign Function and Memory API in [Overview of New Features in Java 19](./java19.md), so no further introduction will be provided here.

## JEP 443: Unnamed Patterns and Variables (Preview)

Unnamed patterns and variables allow us to use the underscore `_` to represent unnamed variables and components not used in pattern matching, aimed at improving code readability and maintainability.

Typical scenarios for unnamed variables include `try-with-resources` statements, exception variables in `catch` clauses, and `for` loops. When a variable does not need to be used, we can substitute it with an underscore `_` to clearly denote an unused variable.

```java
try (var _ = ScopedContext.acquire()) {
  // No use of acquired resource
}
try { ... }
catch (Exception _) { ... }
catch (Throwable _) { ... }

for (int i = 0, _ = runOnce(); i < arr.length; i++) {
  ...
}
```

Unnamed patterns are unconditional patterns that do not bind any value. Unnamed pattern variables appear in type patterns.

```java
if (r instanceof ColoredPoint(_, Color c)) { ... c ... }

switch (b) {
    case Box(RedBall _), Box(BlueBall _) -> processBox(b);
    case Box(GreenBall _)                -> stopProcessing();
    case Box(_)                          -> pickAnotherBox();
}
```

## JEP 444: Virtual Threads

Virtual threads are a significant update that must be taken seriously!

Virtual threads were first previewed in Java 19, proposed by [JEP 425](https://openjdk.org/jeps/425). The second preview occurred in JDK 20. Ultimately, virtual threads were officially added in JDK 21.

[Overview of New Features in Java 20](./java20.md) has detailed virtual threads, so we won't repeat them here.

## JEP 445: Unnamed Classes and Instance Main Methods (Preview)

This feature simplifies the declaration of the `main` method. For beginners in Java, the declaration of this `main` method introduces too many Java syntax concepts, which is not conducive to quickly getting started.

Before using this feature, a `main` method is defined as follows:

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

After using the new feature, a `main` method can be defined as follows:

```java
class HelloWorld {
    void main() {
        System.out.println("Hello, World!");
    }
}
```

Further simplification (unnamed classes allow us not to define a class name):

```java
void main() {
   System.out.println("Hello, World!");
}
```

## References

- Java 21 String Templates: <https://howtodoinjava.com/java/java-string-templates/>
- Java 21 Sequenced Collections: <https://howtodoinjava.com/java/sequenced-collections/>
