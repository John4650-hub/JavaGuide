I don't...
title: Java Reaction Detail
Category:
Tag:

- Java Foundation.
  I don't...

What's reflection?

If you've studied the bottom principles of the framework, or if we've written the framework ourselves, you'll know the concept of reflection.

Reflection is called the soul of the framework largely because it gives us the capacity to analyze the category at the time of operation and to implement the methodology in the category.

By reflecting, you can get all the attributes and methods of any class, and you can call them.

# Do you understand the reflection applications?

Like we usually write business code most of the time, and we're rarely exposed to the direct use of reflection.

However, this does not mean that reflection is useless. On the contrary, it's reflection that makes it so easy to use frameworks. There's a lot of reflection in frameworks like Spring/Spring Boot, MyBatis, etc.

**Dynamic agents are also heavily used in these frameworks, and their realization is also dependent on reflection.**

The following, for example, is an example code for dynamic agents through JDK, which uses the reflection class `Method` to call the specified method.

```
Java
This post is part of our special coverage Global Development 2011.
**
:: Real object in the proxy category
*/
I don't know.

Public DebugInvocationHandler
this.target = target;
♪ I'm sorry ♪

Subject program, Method method, Object[]
System.out.println("before method " + method.getName());
Subject result = method.invoke(target, args);
System.out.println("after method " + method.getName());
I'm sorry.
♪ I'm sorry ♪
♪ I'm sorry ♪

```

In addition, the realization of something like Java's big instrument **Note** uses reflection.

Why did you use Spring when a `@Component` note declared a class as a Spring Bean? Why did you get the value in the configuration file through a `@Value` note? How did it work?

These are because you can base yourself on a reflection analysis class and then get a comment on the parameters of the class/attribute/method. After you get the note, you can do further processing.

# Talk about the strengths and weaknesses of reflection mechanisms

**Advantages**: to make our code more flexible and to facilitate open-boxing of frameworks.

**Shortcomings**: This also adds to the security problem by providing us with the capability to conduct analytical operations. For example, security checks that ignore broad parameters (the security checks of broad parameters occur when compiled). In addition, reflection is almost as close to performance as possible, although it has little practical impact on the framework. Read: [Java Reflection: Why is it so slow?](https://stackoverflow.com/questions/1392351/java-reflection-why-is-it-so-slow)

# Reflection action

## Four ways to get Class objects

If we dynamically get this information, we need to rely on Class objects. The Class class object tells running programs about a class of methods, variables, etc. Java offers four ways to get Class objects:

**1. Specific categories are available:**

```
Java
Class<?> targetClass = TargetObject.class;
```

But we generally don't know the specific category, and basically get the Class object through the type under the package, which does not allow for initialization of the Class object.

**2. Access via the full path of the `Class.forName()` input class:**

```
Java
Class<?> targetClass1 = Class.forName("cn.javaguide.TargetObject");
```

**3. Access to:**

```
Java
TargetObject o = new TargetObject();
Class<?> targetClass2 = o.getClass();
```

**4. Access via a class loader `xxxClassLoader.loadClass()` input class path:**

```
Java
ClassLoader.getSystemClassLoader().loadClass("cn.javaguide.TargetObject");
```

The acquisition of Class objects by type loader will not be initialized, meaning that static blocks and static objects will not be executed without a series of steps including initialization.

# Some basics of reflection

1. Create a class `TargetObject`, which we want to use as reflection.

```
Java
I don't know.

public class TargetObject {
    I don't know.

    public TargetObject() {
        this.value = "JavaGuide";
    }
    ♪ I'm sorry ♪

    public void publicMethod() {
        System.out.println("I love " + value);
    }
    ♪ I'm sorry ♪

    private void privateMethod() {
        System.out.println("value is " + value);
    }
    ♪ I'm sorry ♪
    ♪ I'm sorry ♪
}
```

2. Methods and properties for the use of reflection

```
Java
I don't know.

import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class Main {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, IllegalAccessException, InstantiationException, InvocationTargetException, NoSuchFieldException {
```
