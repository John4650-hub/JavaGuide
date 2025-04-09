I don't...
Title: Java Value Transfer Details
Category:
Tag:
- Java Foundation.
I don't...

Before we begin, let's get to the following two concepts:

- Solid
- Value Transfer & Reference Transfer

# Solid

The definition of the method may be used for **Parameters** (participated method), which are divided into:

- **Parameters (actual parameters, Arguments)**: Parameters for transmission to functions/methods must have a defined value.
- **Shapes (formal parameters, Parameters)**: used to define functions/methods, receiving syntheses without the need for defined values.

```Java
String hello = "Hello!";
// Hello.
sayHello(hello);
// str is the shape
void sayHello(String str) {
    System.out.println(str);
}
```

# Value Transfer & Reference Transfer

There are two ways in which the program design language transmits the reference to the method (or function):

- **Value Transfer**: The method receives a copy of the reference value, which will be created.
- **Reference Transfer**: the method receives directly the address of the object referenced in the stack, does not create a copy, and the modification of the object will affect it.

Many program design languages (e.g., C++, Pascal) provide the means to transfer two parameters, although in Java only values are passed.

# Why Does Java Only Pass Values?

**Why does Java have to pass values?** Don't need too much explanation, I'll prove it with three examples.

### Case 1: Transfer Basic Type Parameters

Code:

```Java
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

```
a = 20
b = 10
num1 = 10
num2 = 20
```

Parsing:

In the `swap()` method, the `a` and `b` values are exchanged without affecting `num1` and `num2`. The values of `a` and `b` are simply copied from `num1` and `num2`. In other words, `a` and `b` are equivalent to copies of `num1` and `num2`, the content of which, however modified, does not affect the original itself.

(https://oss.javaguide.cn/github/javaguide/java/basis/java-value-passing-01.png)

By way of example, we already know that a method cannot modify the parameters of a basic data type, whereas the object references are different as parameters, see Case 2.

### Case 2: Pass Reference Type Parameters

Code:

```Java
public static void main(String[] args) {
    int[] arr = {1, 2, 3, 4, 5};
    System.out.println(arr[0]);
    change(arr);
    System.out.println(arr[0]);
}

public static void change(int[] array) {
    // Convert the first element of the array to zero
    array[0] = 0;
}
```

Output:

```
1
0
```

Parsing:

(https://oss.javaguide.cn/github/javaguide/java/basis/java-value-passing-02.png)

A lot of people who looked at this case must think that Java used a reference transfer for the parameters of the reference type.

Actually, it's not. It's worth noting here that it's a real address!

That is to say, the `change` method is passed the address of `arr` (the actual array) so that it and `arr` refer to the same array of objects. This also explains why changes in the method affect the original.

In order to make a stronger rebuttal of Java's use of non-reference transfers for reference-type parameters, let's look at the following case!

### Case 3: Pass Reference Type Parameter

```Java
public class Person {
    private String name;
    // omit constructor, Getter & Setter methods
}

public static void main(String[] args) {
    Person xiaoZhang = new Person();
    Person xiaoLi = new Person();
    swap(xiaoZhang, xiaoLi);
    System.out.println("xiaoZhang: " + xiaoZhang.getName());
    System.out.println("xiaoLi: " + xiaoLi.getName());
}

public static void swap(Person person1, Person person2) {
    Person temp = person1;
    person1 = person2;
    person2 = temp;
    System.out.println("person1: " + person1.getName());
    System.out.println("