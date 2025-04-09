# final, static, this, super

# Final Keyword

**final keyword, meaning final, non-modifiable, most unalterable, used to modify classes, methods, and variables, with the following characteristics:**

1. The class of final modifications cannot be inherited, and all member methods in the final class are implicitly designated as final;

1. Final Modifier shall not be overridden;

1. The variable of the final modification is constant, and in the case of a variable of the basic data type, the value cannot be changed once it has been initialized; in the case of a variable of the reference type, it cannot be directed to another object after it has been initialized.

Note: There are two reasons for using the final method:

1. To lock down the method in order to prevent any class of inheritance from modifying its meaning;
1. Efficiency. In the early Java implementation version, the final method is converted to inline calls. But if the method is too large, it may not be possible to see any enhancement of the performance resulting from the inline call (the current Java version no longer requires these optimizations using the final method).

# Static Keyword

**static keywords have four main use scenarios:**

1. **The member variables and methods of the class are modified:** The members that are modified by static belong to a class, do not belong to an object in a single class, are shared by all objects in the class, and can and are recommended to be called through class names. The member variable declared by static is a static member variable, which is stored in the method area of the Java heap. Call format: `class name.static variable name` or `class name.static method()`.

1. **Static code block:** Static code block is defined outside the class method, and the static code block is executed prior to the non-static code block (static block -> non-static block -> constructor method). No matter how many objects are created, the static block is executed only once.

1. **Static inner class (static members can only be qualified by inner class):** One of the largest differences between static inner class and non-static inner class is that non-static inner class may implicitly hold a reference to its enclosing class after compilation, but the static inner class does not. Without this reference, it means that its creation does not need to rely on the creation of the enclosing class. It cannot use non-static member variables and methods of any enclosing class.

1. **Static import (used to import static resources in class, new features after 1.5):** Format: `import static` with two keywords that specify the designated static resource in class to be imported and that do not require the use of a class name to call a static member in class, and can directly use the static member variable and method in class.

# This keyword

The this keyword is used in the current instance of the class. For example:

```java
class Manager {
    Employees[] employees;
    void manageEmployees() {
        int totalEmp = this.employees.length;
        System.out.println(totalEmp);
        this.report();
    }
    
    void report() {
        // report logic
    }
}
```

In the example above, the this keyword is used in two places:

- `this.employees.length`: Variable for accessing the Manager class of current instance.
- `this.report()`: The method for calling the current instance of the Manager class.

The this keyword is optional, which means that the example above is the same without the keyword being used. However, the use of this keyword may make the code easier to read or understand.

# super keyword

The super keyword is used to access parent variables and methods from subclasses. For example:

```java
class Super {
    String name;
    void showNumber() {
        System.out.println("name = " + name);
    }
}

public class Sub extends Super {
    void bar() {
        this.name = "10";
        super.showNumber();
    }
}
```

In the above example, the Sub class accesses the parent member variable name and calls the `showNumber()` method from its parent class Super.

**Use this and super with attention:**

- When `super()` calls other constructors in the parent class in the constructor, the statement must be at the first line of the constructor; otherwise, the compiler will throw an error. Also, `this` must be placed in the first line when calling other constructors in this class.
- `this` and `super` cannot be used in static methods.

**Brief explanation:**

A member with a static modifier belongs to a class, does not belong to an object in a single class, and is shared by all objects in the class. `this` represents the reference to the object in this class, which refers to the object in this class; whereas `super` represents the reference to the parent object, which refers to the parent object; therefore, \*\*`this` and `super` are within the scope of the object, while the static method
