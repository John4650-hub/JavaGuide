# Summary of final, static, this, super Keywords

## final Keyword

**The final keyword means final and unmodifiable, it represents the least amount of change and is used to modify classes, methods, and variables with the following characteristics:**

1. A class modified with final cannot be inherited, and all member methods in a final class will implicitly be designated as final methods;
1. A method modified with final cannot be overridden;
1. A variable modified with final is a constant. If it is a primitive data type, its value cannot be changed after initialization; if it is a reference type, it cannot point to another object after initialization.

Note: There are two reasons to use final methods:

1. To lock the method to prevent any inherited class from modifying its meaning;
1. Efficiency. In earlier versions of Java, final methods would be converted into inline calls. However, if the method is too large, no performance improvement from inlining may be seen (current Java versions no longer require using final methods for such optimizations).

## static Keyword

**The static keyword mainly has the following four usage scenarios:**

1. **Modifying member variables and member methods:** Members modified with static belong to the class and not to a specific instance of this class, shared by all objects in the class, and it is recommended to call them through the class name. Member variables declared as static are static member variables, which are stored in the method area of the Java memory region. Invocation format: `ClassName.staticVariableName` `ClassName.staticMethodName()`
1. **Static code blocks:** Static code blocks are defined outside of class methods and are executed before non-static code blocks (static code blocks → non-static code blocks → constructors). Regardless of how many objects are created, a static code block is executed only once.
1. **Static inner classes (only classes that are static can modify inner classes):** There is one significant difference between static inner classes and non-static inner classes: non-static inner classes implicitly hold a reference to the enclosing class after compilation, while static inner classes do not. The absence of this reference means: 1. Its creation does not depend on the enclosing class's creation. 2. It cannot use any non-static member variables and methods of the enclosing class.
1. **Static import (used to import static resources from a class, new feature after 1.5):** The format is: `import static` these two keywords can be used together to specify importing certain static resources from a class, and there is no need to use the class name to call static members; static member variables and methods can be accessed directly.

## this Keyword

The this keyword is used to reference the current instance of the class. For example:

```java
class Manager {
    Employees[] employees;
    void manageEmployees() {
        int totalEmp = this.employees.length;
        System.out.println("Total employees: " + totalEmp);
        this.report();
    }
    void report() { }
}
```

In the example above, the this keyword is used in two places:

- this.employees.length: Accessing the variable of the current instance of the Manager class.
- this.report(): Calling the method of the current instance of the Manager class.

This keyword is optional, meaning that the example above would behave the same without using this keyword. However, using this keyword may make the code more readable or understandable.

## super Keyword

The super keyword is used to access variables and methods of the parent class from a subclass. For example:

```java
public class Super {
    protected int number;
    protected void showNumber() {
        System.out.println("number = " + number);
    }
}
public class Sub extends Super {
    void bar() {
        super.number = 10;
        super.showNumber();
    }
}
```

In the example above, the Sub class accesses the superclass member variable number and calls its parent class Super's `showNumber()` method.

**Issues to note when using this and super:**

- When using `super()` to call another constructor in the parent class, this statement must be the first line of the constructor; otherwise, the compiler will throw an error. Similarly, when calling another constructor within the same class using this, it should also be placed at the first line.
- this and super cannot be used in static methods.

**In brief:**

Members modified with static belong to the class and are shared by all objects within the class, while this represents a reference to the current class object and points to that instance; super represents a reference to the parent class object and points to that parent; thus, **this and super pertain to the instance realm, whereas static methods pertain to the class realm.**

## References

- <https://www.codejava.net/java-core/the-java-language/java-keywords>
- <https://blog.csdn.net/u013393958/article/details/79881037>

# Detailed Explanation of the static Keyword

## The static keyword mainly has the following four usage scenarios

1. Modifying member variables and methods
1. Static code blocks
1. Modifying classes (only inner classes)
1. Static import (used to import static resources from a class, new feature after 1.5)

### Modifying member variables and methods (Commonly used)

Members modified with static belong to the class and not to a specific instance of this class, shared by all objects in the class, and it is recommended to call them through the class name. Member variables declared as static are static member variables, which are stored in the method area of the Java memory region.

The method area, like the Java heap, is a memory area shared among threads and is used to store information about classes that have been loaded by the virtual machine, constants, static variables, and compiled code from the just-in-time compiler. Although the Java Virtual Machine specification describes the method area as a logical part of the heap, it is also known as Non-Heap; this is to distinguish it from the Java heap area.

In the HotSpot virtual machine, the method area is often referred to as "Permanent Generation," but the two are not equivalent. It is merely because the team that designed the HotSpot virtual machine implemented the method area using Permanent Generation so that the garbage collector of the HotSpot virtual machine can manage this memory as it does with the Java heap. However, this is not a good idea as it can lead to memory overflow issues.

Invocation format:

- `ClassName.staticVariableName`
- `ClassName.staticMethodName()`

If a variable or method is marked as private, it means that this attribute or method can only be accessed inside the class and not from outside.

Test method:

```java
public class StaticBean {
    String name;
    // Static variable
    static int age;
    public StaticBean(String name) {
        this.name = name;
    }
    // Static method
    static void sayHello() {
        System.out.println("Hello, I am Java");
    }
    @Override
    public String toString() {
        return "StaticBean{"+
                "name=" + name + ", age=" + age +
                "}";
    }
}
```

```java
public class StaticDemo {
    public static void main(String[] args) {
        StaticBean staticBean1 = new StaticBean("1");
        StaticBean staticBean2 = new StaticBean("2");
        StaticBean staticBean3 = new StaticBean("3");
        StaticBean staticBean4 = new StaticBean("4");
        StaticBean.age = 33;
        System.out.println(staticBean1 + " " + staticBean2 + " " + staticBean3 + " " + staticBean4);
        // StaticBean{name=1, age=33} StaticBean{name=2, age=33} StaticBean{name=3, age=33} StaticBean{name=4, age=33}
        StaticBean.sayHello(); // Hello, I am Java
    }
}
```

### Static Code Blocks

Static code blocks are defined outside of class methods. Static code blocks are executed before non-static code blocks (static code blocks → non-static code blocks → constructors). Regardless of how many objects are created, a static code block is executed only once.

The format of a static code block is:

```plain
static {
    statement;
}
```

A class can have multiple static code blocks, which can be placed anywhere within the class. The JVM executes these static code blocks when loading the class. If there are multiple static code blocks, the JVM will execute them in the order they appear in the class. Each block is executed only once.

![](https://oss.javaguide.cn/github/javaguide/88531075.jpg)

Static code blocks can assign values to static variables defined after them but cannot access them.

### Static Inner Class

The biggest difference between static inner classes and non-static inner classes is that non-static inner classes implicitly save a reference to the enclosing class after compilation, while static inner classes do not. The absence of this reference implies:

1. Its creation does not depend on the creation of the enclosing class.
1. It cannot use any non-static member variables and methods of the enclosing class.

Example (Static inner class implementing singleton pattern):

```java
public class Singleton {
    // Declared as private to prevent calling the default constructor to create an object
    private Singleton() {
    }
    // Declared as private to indicate that the static inner class can only be accessed within the Singleton class
    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
    public static Singleton getUniqueInstance() {
        return SingletonHolder.INSTANCE;
    }
}
```

When the Singleton class is loaded, the static inner class SingletonHolder is not loaded into memory. Only when calling the `getUniqueInstance()` method, triggering `SingletonHolder.INSTANCE`, will SingletonHolder be loaded, initializing the INSTANCE. The JVM ensures that INSTANCE is instantiated only once.

This method not only provides the advantage of lazy initialization but also offers thread safety support from the JVM.

### Static Import

The format is: import static

Using these two keywords together allows specifying importing specific static resources from a class without using the class name to call static members; thus, static member variables and methods can be accessed directly.

```java
// Importing all static resources from Math, allowing direct usage of its static methods without class name
// If you only want to import a specific static method, just replace * with the method name accordingly
import static java.lang.Math.*; // Alternatively, you may use import static java.lang.Math.max; for the same effect
public class Demo {
  public static void main(String[] args) {
    int max = max(1, 2);
    System.out.println(max);
  }
}
```

## Supplementary Content

### Static Methods vs Non-Static Methods

Static methods belong to the class itself, while non-static methods belong to each object instantiated from the class. If your method performs operations independent of its instance variables and methods, consider making it static (this will reduce space consumption of the program). Otherwise, it should be non-static.

Example:

```java
class Foo {
    int i;
    public Foo(int i) {
       this.i = i;
    }
    public static String method1() {
       return "An example string that doesn't depend on i (an instance variable)";
    }
    public int method2() {
       return this.i + 1;  // Depends on i
    }
}
```

You can call static methods like this: `Foo.method1()`. If you try calling method2 this way, it will fail. However, the following is acceptable:

```java
Foo bar = new Foo(1);
bar.method2();
```

Summary:

- When calling static methods externally, you can use either "ClassName.MethodName" or "ObjectName.MethodName" syntax. However, instance methods only support the latter. In other words, static methods can be accessed without creating objects.
- In accessing members of the class, static methods are only allowed to access static members (i.e., static member variables and static methods) and cannot access instance member variables and instance methods; instance methods do not have this restriction.

### Differences Between `static{}` Static Code Blocks and `{}` Non-Static Code Blocks (Constructor Code Blocks)

Similarities: Both are executed when the JVM loads the class and before the constructor executes; multiple instances can be defined, executed in the order defined, and are generally used for initializing some static variables.

Differences: Static code blocks execute before non-static code blocks (static code block → non-static code block → constructor). A static code block only executes once during the first instantiation, while non-static code blocks execute every time a new instance is created. Non-static blocks can be defined within ordinary methods (though their utility is limited), while static code blocks cannot.

> **🐛 Correction (refer to: [issue #677](https://github.com/Snailclimb/JavaGuide/issues/677))**: Static code blocks may be executed during the first instantiation of an object but are not guaranteed to execute only once upon instantiation. For example, executing `Class.forName("ClassDemo")` also triggers their execution; thus, both instantiation and `Class.forName("ClassDemo")` will lead to the static block execution.
> Generally, if certain code, such as commonly used project variables or objects, must execute when the project starts, static blocks should be used. Such code is executed automatically. Conversely, to design methods that can be called without creating an object, such as the `Arrays`, `Character`, or `String` classes, static methods must be employed. The distinction is that static blocks execute automatically, while static methods only execute when called.

Example:

```java
public class Test {
    public Test() {
        System.out.print("Default constructor! --");
    }
    // Non-static code block
    {
        System.out.print("Non-static code block! --");
    }
    // Static code block
    static {
        System.out.print("Static code block! --");
    }
    private static void test() {
        System.out.print("Content in static method! --");
        {
            System.out.print("Code block in static method! --");
        }
    }
    public static void main(String[] args) {
        Test test = new Test();
        Test.test(); // Static code block! -- Content in static method! -- Code block in static method! --
    }
}
```

The output of the above code is:

```plain
Static code block! -- Non-static code block! -- Default constructor! -- Content in static method! -- Code block in static method! --
```

When only executing `Test.test();`:

```plain
Static code block! -- Content in static method! -- Code block in static method! --
```

When only executing `Test test = new Test();`:

```plain
Static code block! -- Non-static code block! -- Default constructor! --
```

The difference between non-static blocks and constructors is that non-static blocks serve to initialize all objects uniformly, whereas constructors initialize corresponding objects. Given that there can be multiple constructors, the executed constructor determines the created object, but regardless of which object is established, the same constructor code block will always execute, meaning the initialization contents defined in the constructor block are common for different objects.

### References

- <https://blog.csdn.net/chen13579867831/article/details/78995480>
- <https://www.cnblogs.com/chenssy/p/3388487.html>
- <https://www.cnblogs.com/Qian123/p/5713440.html>

<!-- @include: @article-footer.snippet.md -->
