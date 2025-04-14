---
title: Detailed Explanation of Java Value Passing
category: Java
tag:
  - Java Basics
---

Before we begin, let's clarify the following two concepts:

- Formal Parameters & Actual Parameters
- Value Passing & Reference Passing

## Formal Parameters & Actual Parameters

The definition of a method may involve **parameters** (methods with parameters), which are categorized in programming languages as:

- **Actual Parameters (Arguments)**: Parameters passed to a function/method that must have a definite value.
- **Formal Parameters (Parameters)**: Used to define a function/method, receiving actual parameters, and do not need to have a definite value.

```java
String hello = "Hello!";
// hello is the actual parameter
sayHello(hello);
// str is the formal parameter
void sayHello(String str) {
    System.out.println(str);
}
```

## Value Passing & Reference Passing

Programming languages categorize the way actual parameters are passed to methods (or functions) into two types:

- **Value Passing**: The method receives a copy of the actual parameter's value, creating a duplicate.
- **Reference Passing**: The method directly receives the address of the object referenced by the actual parameter in the heap, without creating a duplicate. Modifications to the formal parameter will affect the actual parameter.

Many programming languages (like C++, Pascal) provide both ways of parameter passing; however, in Java, only value passing is available.

## Why Does Java Only Have Value Passing?

**Why do we say Java only has value passing?** Without further ado, I will demonstrate this with three examples.

### Case 1: Passing Primitive Type Parameters

Code:

```java
public static void main(String[] args) {
    int num1 = 10;
    int num2 = 20;
    swap(num1, num2);
    System.out.println("num1 = " + num1);
    System.out.println("num2 = " + num2);
}

public static void swap(int a, int b) {
    int temp = a;
    a = b;
    b = temp;
    System.out.println("a = " + a);
    System.out.println("b = " + b);
}
```

Output:

```plain
a = 20
b = 10
num1 = 10
num2 = 20
```

Analysis:

In the `swap()` method, the values of `a` and `b` are swapped, but this does not affect `num1` and `num2`. This is because the values of `a` and `b` are merely copies of `num1` and `num2`. In other words, `a` and `b` are copies of `num1` and `num2`, and any modifications to the copies do not affect the original values.

![](https://oss.javaguide.cn/github/javaguide/java/basis/java-value-passing-01.png)

From the above example, we understand that a method cannot modify a parameter of a primitive data type. However, passing object references as parameters is different; let's look at Case 2.

### Case 2: Passing Reference Type Parameters 1

Code:

```java
public static void main(String[] args) {
    int[] arr = { 1, 2, 3, 4, 5 };
    System.out.println(arr[0]);
    change(arr);
    System.out.println(arr[0]);
}

public static void change(int[] array) {
    // Change the first element of the array to 0
    array[0] = 0;
}
```

Output:

```plain
1
0
```

Analysis:

![](https://oss.javaguide.cn/github/javaguide/java/basis/java-value-passing-02.png)

After seeing this case, many people might think that Java uses reference passing for reference type parameters.

In fact, that is not the case; what is passed is still a value, but this value is the address of the actual parameter!

This means that the parameter of the `change` method is a copy of the address of `arr` (the actual parameter), so it points to the same array object as `arr`. This explains why modifications to the formal parameter affect the actual parameter.

To further refute the idea that Java uses reference passing for reference type parameters, let's look at the next case!

### Case 3: Passing Reference Type Parameters 2

```java
public class Person {
    private String name;
   // Omitted constructor, Getter & Setter methods
}

public static void main(String[] args) {
    Person xiaoZhang = new Person("小张");
    Person xiaoLi = new Person("小李");
    swap(xiaoZhang, xiaoLi);
    System.out.println("xiaoZhang:" + xiaoZhang.getName());
    System.out.println("xiaoLi:" + xiaoLi.getName());
}

public static void swap(Person person1, Person person