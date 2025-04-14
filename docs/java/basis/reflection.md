---
title: Java Reflection Mechanism Detailed Explanation
category: Java
tag:
  - Java Basics
---

## What is Reflection?

If you have studied the underlying principles of frameworks or have written your own frameworks, you must be familiar with the concept of reflection.

Reflection is called the soul of frameworks mainly because it gives us the ability to analyze classes and execute methods in those classes at runtime.

With reflection, you can obtain all properties and methods of any class, and you can also invoke these methods and access these properties.

## Do You Know the Application Scenarios of Reflection?

Most of the time, we are writing business code and rarely encounter scenarios where we directly use reflection.

However, this does not mean that reflection is useless. On the contrary, it is precisely because of reflection that you can easily use various frameworks. Frameworks like Spring/Spring Boot, MyBatis, etc., make extensive use of reflection.

**These frameworks also use dynamic proxies extensively, and the implementation of dynamic proxies relies on reflection.**

For example, below is sample code that implements dynamic proxies using JDK, where the reflection class `Method` is used to invoke the specified method.

```java
public class DebugInvocationHandler implements InvocationHandler {
    /**
     * The real object in the proxy class
     */
    private final Object target;

    public DebugInvocationHandler(Object target) {
        this.target = target;
    }

    public Object invoke(Object proxy, Method method, Object[] args) throws InvocationTargetException, IllegalAccessException {
        System.out.println("before method " + method.getName());
        Object result = method.invoke(target, args);
        System.out.println("after method " + method.getName());
        return result;
    }
}
```

Additionally, one of Java's powerful features, **annotations**, also relies on reflection for its implementation.

Why is it that when you use Spring, a `@Component` annotation declares a class as a Spring Bean? Why can you read values from a configuration file using a `@Value` annotation? How does this work?

All of this is because you can analyze classes based on reflection and retrieve annotations from classes, properties, methods, and their parameters. Once you retrieve the annotations, you can perform further processing.

## Discussing the Advantages and Disadvantages of Reflection Mechanism

**Advantages**: Allows our code to be more flexible and provides convenience for various frameworks to offer out-of-the-box functionality.

**Disadvantages**: Gives us the ability to analyze operational classes at runtime, which also increases security issues. For example, it can bypass safety checks for generic parameters (safety checks for generic parameters occur at compile time). Additionally, the performance of reflection is somewhat lower, but it does not significantly affect the frameworks in practice. Related reading: [Java Reflection: Why is it so slow?](https://stackoverflow.com/questions/1392351/java-reflection-why-is-it-so-slow)

## Reflection Practice

### Four Ways to Obtain Class Objects

If we want to dynamically obtain this information, we need to rely on the Class object. The Class object provides information about a class's methods, variables, and other details to the running program. Java provides four ways to obtain a Class object:

**1. When you know the specific class:**

```java
Class alunbarClass = TargetObject.class;
```

However, we usually do not know the specific class and typically obtain Class objects by traversing through the classes in a package. Obtaining a Class object in this way does not involve initialization.

**2. Use `Class.forName()` to get the full path of the class:**

```java
Class alunbarClass1 = Class.forName("cn.javaguide.TargetObject");
```

**3. Obtain via an object instance using `instance.getClass()`:**

```java
TargetObject o = new TargetObject();
Class alunbarClass2 = o.getClass();
```

**4. Obtain via class loader using `xxxClassLoader.loadClass()`:**

```java
ClassLoader.getSystemClassLoader().loadClass("cn.javaguide.TargetObject");
```

Obtaining a Class object through the class loader does not involve initialization, meaning it does not perform a series of steps including initialization, and static code blocks and static objects will not execute.

### Some Basic Operations of Reflection

1. Create a class we want to use reflection with called `TargetObject`.

```java
package cn.javaguide;

public class TargetObject {
    private String value;

    public TargetObject() {
        value = "JavaGuide";
    }

    public void publicMethod(String s) {
        System.out.println("I love " + s);
    }

    private void privateMethod() {
        System.out.println("value is " + value);
    }
}
```

2. Use reflection to operate on the methods and properties of this class.

```java
package cn.javaguide;

import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class Main {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, IllegalAccessException, InstantiationException, InvocationTargetException, NoSuchFieldException {
        /**
         * Obtain the Class object of TargetObject and create an instance of TargetObject
         */
        Class<?> targetClass = Class.forName("cn.javaguide.TargetObject");
        TargetObject targetObject = (TargetObject) targetClass.newInstance();
        /**
         * Get all methods defined in the TargetObject class
         */
        Method[] methods = targetClass.getDeclaredMethods();
        for (Method method : methods) {
            System.out.println(method.getName());
        }

        /**
         * Obtain a specific method and invoke it
         */
        Method publicMethod = targetClass.getDeclaredMethod("publicMethod",
                String.class);

        publicMethod.invoke(targetObject, "JavaGuide");

        /**
         * Obtain a specific parameter and modify it
         */
        Field field = targetClass.getDeclaredField("value");
        // To modify the parameter in the class, we bypass the security check
        field.setAccessible(true);
        field.set(targetObject, "JavaGuide");

        /**
         * Invoke the private method
         */
        Method privateMethod = targetClass.getDeclaredMethod("privateMethod");
        // To invoke the private method, we bypass the security check
        privateMethod.setAccessible(true);
        privateMethod.invoke(targetObject);
    }
}
```

Output:

```plain
publicMethod
privateMethod
I love JavaGuide
value is JavaGuide
```

**Note**: Some readers have mentioned that the above code will throw a `ClassNotFoundException`. The reason is that you have not replaced the package name in the following line of code with the package where your created `TargetObject` resides.
You can refer to: <https://www.cnblogs.com/chanshuyi/p/head_first_of_reflection.html> for this article.

```java
Class<?> targetClass = Class.forName("cn.javaguide.TargetObject");
```

<!-- @include: @article-footer.snippet.md -->
