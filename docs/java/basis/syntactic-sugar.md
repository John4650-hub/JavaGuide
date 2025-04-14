---
title: Detailed Explanation of Java Syntactic Sugar
category: Java
tag:
  - Java Basics
head:
  -   - meta
      - name: keywords
        content: Java Syntactic Sugar
  -   - meta
      - name: description
        content: This article introduces 12 commonly used syntactic sugars in Java. Syntactic sugar refers to syntax that makes it easier for developers to code. However, this syntax is only recognized by developers. To be executed, it needs to be desugared, meaning it is converted to a syntax understood by the JVM. Once we desugar these syntactic sugars, we realize that many of the convenient syntaxes we use daily are actually composed of simpler syntactic structures. With these syntactic sugars, we can greatly improve efficiency in daily development, but we must also avoid overusing them. It’s best to understand the principles before using them, to avoid pitfalls.
---

> Author: Hollis
>
> Original: <https://mp.weixin.qq.com/s/o4XdEMq1DL-nBS-f8Za5Aw>

Syntactic sugar is a common topic in Java interviews at major companies.

This article delves into the principles and usages of syntactic sugar in Java from the perspective of Java compilation, exploring bytecode and class files to help everyone learn how to use Java syntactic sugar while understanding the principles behind it.

## What is Syntactic Sugar?

**Syntactic Sugar** refers to a term invented by British computer scientist Peter J. Landin, indicating certain syntax added to a programming language which does not affect the functionality of the language but makes it more convenient for programmers to use. In short, syntactic sugar makes programs more concise and readable.

![](https://oss.javaguide.cn/github/javaguide/java/basis/syntactic-sugar/image-20220818175953954.png)

> Interestingly, in the programming field, in addition to syntactic sugar, there are terms like "syntactic salt" and "syntactic sugar spirit," but we will not expand on that here due to space constraints.

Almost all programming languages we are familiar with contain some form of syntactic sugar. The author believes that the amount of syntactic sugar is one of the criteria for judging how impressive a language is. Many people say that Java is a "low-sugar language," but since Java 7, various sugars have been added at the language level, primarily developed under the "Project Coin" initiative. Despite some considering current Java to be low-sugar, it is expected to continue evolving towards "high-sugar" in the future.

## What Common Syntactic Sugars Exist in Java?

As mentioned earlier, the existence of syntactic sugar primarily serves to facilitate developers. However, **the Java Virtual Machine does not support these syntactic sugars. These syntactic sugars will be restored to simple basic syntactic structures during the compilation phase, a process known as desugaring.**

Speaking of compilation, we all know that in Java, the `javac` command can compile source files with the `.java` suffix into bytecode with the `.class` suffix that can run on the Java Virtual Machine. If you look at the source code of `com.sun.tools.javac.main.JavaCompiler`, you will find that there is a step in `compile()` where it calls `desugar()`, which is responsible for implementing the desugaring of syntactic sugar.

The most commonly used syntactic sugars in Java include generics, varargs, conditional compilation, automatic boxing and unboxing, inner classes, etc. This article will primarily analyze the principles behind these syntactic sugars, peeling off the layers of sugar step by step to reveal their essence.

We will utilize [decompilation](https://mp.weixin.qq.com/s?__biz=MzI3NzE0NjcwMg==&mid=2650120609&idx=1&sn=5659f96310963ad57d55b48cee63c788&chksm=f36bbc80c41c3596a1e4bf9501c6280481f1b9e06d07af354474e6f3ed366fef016df673a7ba&scene=21#wechat_redirect), where you can perform online decompilation of class files through [Decompilers online](http://www.javadecompilers.com/).

### Switch Supporting String and Enum

As mentioned earlier, starting from Java 7, the syntax sugar in Java has become increasingly rich, one notable addition being that `switch` now supports `String`.

Before we begin, let's clarify that the `switch` statement in Java originally only supports primitive types such as `int`, `char`, etc. For the `int` type, values are compared directly. For the `char` type, the comparison is made using ASCII values. Thus, for the compiler, `switch` can only effectively use integer types; any type being compared must be converted to integer, such as `byte`, `short`, and `char` (ASCII codes are integers), along with `int`.

Now let's look at the support for `String` in `switch` with the following code:

```java
public class switchDemoString {
    public static void main(String[] args) {
        String str = "world";
        switch (str) {
        case "hello":
            System.out.println("hello");
            break;
        case "world":
            System.out.println("world");
            break;
        default:
            break;
        }
    }
}
```

The decompiled content is as follows:

```java
public class switchDemoString
{
    public switchDemoString()
    {
    }
    public static void main(String args[])
    {
        String str = "world";
        String s;
        switch((s = str).hashCode())
        {
        default:
            break;
        case 99162322:
            if(s.equals("hello"))
                System.out.println("hello");
            break;
        case 113318802:
            if(s.equals("world"))
                System.out.println("world");
            break;
        }
    }
}
```

From this code, we can see that **the string `switch` is implemented using `equals()` and `hashCode()` methods.** Luckily, the `hashCode()` method returns an `int`, not `long`.

Looking closely, it can be seen that the actual `switch` operation is on the hash value, then safe checks are performed using the `equals` method. This check is necessary because hash collisions can occur. Therefore, its performance is not as efficient as using enums in `switch` or using pure integer constants, but it's not overly inefficient either.

### Generics

We know that many languages support generics, but not everyone knows that different compilers handle generics in distinct ways. Typically, a compiler can use two strategies for generics: `Code specialization` and `Code sharing`. C++ and C# utilize `Code specialization`, while Java utilizes `Code sharing`.

> The `Code sharing` method creates a unique bytecode representation for each generic type, mapping instances of that generic type to this unique representation. Mapping multiple generic class instances to a single bytecode representation is achieved through type erasure.

In other words, **the Java Virtual Machine does not recognize syntax such as `Map<String, String> map`. It needs to desugar the syntax during the compilation phase through type erasure.**

The main process of type erasure is as follows: 1. Replace all generic parameters with their leftmost bounds (the top-level parent type). 2. Remove all type parameters.

The following code:

```java
Map<String, String> map = new HashMap<String, String>();
map.put("name", "hollis");
map.put("wechat", "Hollis");
map.put("blog", "www.hollischuang.com");
```

After type erasure will be transformed into:

```java
Map map = new HashMap();
map.put("name", "hollis");
map.put("wechat", "Hollis");
map.put("blog", "www.hollischuang.com");
```

The following code:

```java
public static <A extends Comparable<A>> A max(Collection<A> xs) {
    Iterator<A> xi = xs.iterator();
    A w = xi.next();
    while (xi.hasNext()) {
        A x = xi.next();
        if (w.compareTo(x) < 0)
            w = x;
    }
    return w;
}
```

After type erasure will become:

```java
 public static Comparable max(Collection xs){
    Iterator xi = xs.iterator();
    Comparable w = (Comparable)xi.next();
    while(xi.hasNext())
    {
        Comparable x = (Comparable)xi.next();
        if(w.compareTo(x) < 0)
            w = x;
    }
    return w;
}
```

**There are no generics within the virtual machine, only plain classes and methods. All generic class type parameters are eradicated at compile time, and generic classes do not have their unique `Class` objects. For instance, there is no `List<String>.class` or `List<Integer>.class`, but only `List.class`.**

### Auto-boxing and Unboxing

Auto-boxing is when Java automatically converts a primitive type value into the corresponding object, such as converting an `int` variable into an `Integer` object. This process is known as boxing, while the opposite, converting an `Integer` object back into an `int` type value, is known as unboxing. Because both boxing and unboxing are automatically performed (not manually), they are referred to as auto-boxing and unboxing. The primitive types `byte`, `short`, `char`, `int`, `long`, `float`, `double`, and `boolean` correspond to the wrapper classes `Byte`, `Short`, `Character`, `Integer`, `Long`, `Float`, `Double`, `Boolean`.

First, let’s look at some code for auto-boxing:

```java
 public static void main(String[] args) {
    int i = 10;
    Integer n = i;
}
```

The decompiled code looks like this:

```java
public static void main(String args[])
{
    int i = 10;
    Integer n = Integer.valueOf(i);
}
```

Now, let’s look at some code for auto-unboxing:

```java
public static void main(String[] args) {

    Integer i = 10;
    int n = i;
}
```

The decompiled code is as follows:

```java
public static void main(String args[])
{
    Integer i = Integer.valueOf(10);
    int n = i.intValue();
}
```

From the decompiled content, we can observe that during boxing, the `Integer`'s `valueOf(int)` method is auto-invoked. In the unboxing case, the `Integer`'s `intValue` method is called automatically.

Thus, **the boxing process is carried out by invoking the wrapper's `valueOf` method, while the unboxing process is implemented by calling the wrapper's `xxxValue` method.**

### Varargs (Variable-Length Arguments)

Varargs were introduced in Java 1.5 and allow a method to accept an arbitrary number of values as parameters.

Look at the following code with varargs, where the `print` method accepts variable-length arguments:

```java
public static void main(String[] args)
    {
        print("Holis", "Public account: Hollis", "Blog: www.hollischuang.com", "QQ: 907607222");
    }

public static void print(String... strs)
{
    for (int i = 0; i < strs.length; i++)
    {
        System.out.println(strs[i]);
    }
}
```

The decompiled code is:

```java
 public static void main(String args[])
{
    print(new String[] {
        "Holis", "\u516C\u4F17\u53F7:Hollis", "\u535A\u5BA2\uFF1Awww.hollischuang.com", "QQ\uFF1A907607222"
    });
}

public static transient void print(String strs[])
{
    for(int i = 0; i < strs.length; i++)
        System.out.println(strs[i]);

}
```

From the decompiled code, we can see that when varargs are used, an array is first created with a length equal to the number of actual parameters passed to the method, then the parameter values are placed into this array, and this array is subsequently passed as a parameter to the called method. (Note: `transient` is meaningful only when modifying member variables; here, "method" is due to using the same value to represent `transient` and `vararg` in `javassist`; see [here](https://github.com/jboss-javassist/javassist/blob/7302b8b0a09f04d344a26ebe57f29f3db43f2a3e/src/main/javassist/bytecode/AccessFlag.java#L32).)

### Enum

Java SE5 introduced a new type - Java’s enum type. The `enum` keyword allows creating a new type with a finite set of named values, which can be used as regular programming components, a very useful feature.

To see the source code, we first need to have a class, so what kind of class is the enum type? Is it `enum`? The obvious answer is no; `enum` is just a keyword like `class`, it is not a class. So, which class maintains the enum? Let’s simply write an enum:

```java
public enum t {
    SPRING,SUMMER;
}
```

Now, let’s decompile it to see how it is implemented. The decompiled code looks like this:

```java
public final class T extends Enum
{
    private T(String s, int i)
    {
        super(s, i);
    }
    public static T[] values()
    {
        T at[];
        int i;
        T at1[];
        System.arraycopy(at = ENUM$VALUES, 0, at1 = new T[i = at.length], 0, i);
        return at1;
    }

    public static T valueOf(String s)
    {
        return (T)Enum.valueOf(demo/T, s);
    }

    public static final T SPRING;
    public static final T SUMMER;
    private static final T ENUM$VALUES[];
    static
    {
        SPRING = new T("SPRING", 0);
        SUMMER = new T("SUMMER", 1);
        ENUM$VALUES = (new T[] {
            SPRING, SUMMER
        });
    }
}
```

From the decompiled code, we can see that `public final class T extends Enum` indicates this class extends `Enum`, and the `final` keyword tells us that this class cannot be further extended.

**When we use `enum` to define an enumeration type, the compiler automatically creates a `final` type class that extends `Enum`, which is why enumeration types cannot be inherited.**

### Inner Class

An inner class, also known as a nested class, can be understood as a regular member of an outer class.

**The reason inner classes are also syntactic sugar is that they are merely a compile-time concept. The `outer.java` file defining an inner class `inner`, once compiled, generates two completely different `.class` files: `outer.class` and `outer$inner.class`. Thus, the inner class name can coincide with that of its outer class.**

```java
public class OutterClass {
    private String userName;

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public static void main(String[] args) {

    }

    class InnerClass{
        private String name;

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }
    }
}
```

After compiling, two class files will be generated: `OutterClass$InnerClass.class` and `OutterClass.class`. When we try to decompile the `OutterClass.class` file, the command line will print the following: `Parsing OutterClass.class...Parsing inner class OutterClass$InnerClass.class... Generating OutterClass.jad`. It will decompile both files and generate an `OutterClass.jad` file with the following content:

```java
public class OutterClass
{
    class InnerClass
    {
        public String getName()
        {
            return name;
        }
        public void setName(String name)
        {
            this.name = name;
        }
        private String name;
        final OutterClass this$0;

        InnerClass()
        {
            this.this$0 = OutterClass.this;
            super();
        }
    }

    public OutterClass()
    {
    }
    public String getUserName()
    {
        return userName;
    }
    public void setUserName(String userName){
        this.userName = userName;
    }
    public static void main(String args1[])
    {
    }
    private String userName;
}
```

**Why can inner classes access the private attributes of the outer class?**

We can add a method in `InnerClass` to print the `userName` property of the outer class:

```java
// Omitted other properties
public class OutterClass {
    private String userName;
    ......
    class InnerClass{
    ......
        public void printOut(){
            System.out.println("Username from OutterClass:" + userName);
        }
    }
}

// At this time, using the javap -p command to decompile the result of OutterClass:
public classOutterClass {
    private String userName;
    ......
    static String access$000(OutterClass);
}
// At this time, the decompiled result of InnerClass:
class OutterClass$InnerClass {
    final OutterClass this$0;
    ......
    public void printOut();
}

```

In reality, after compilation, the `inner` instance internally holds a reference to the `outer` instance named `this$0`, but simply accessing `outer.name` cannot reach private attributes. From the decompiled result, we can see that the outer class has a bridge method `static String access$000(OutterClass)`, which happens to return the `String` type that is the `userName` property. This method is what allows the inner class to access the private attributes of the outer class. Hence, the decompiled `printOut()` method roughly looks like this:

```java
public void printOut() {
    System.out.println("Username from OutterClass:" + OutterClass.access$000(this.this$0));
}
```

Supplementary Information:

1. Anonymous inner classes, local inner classes, and static inner classes also utilize bridge methods to access private attributes.
1. Static inner classes do not have the reference `this$0`.
1. Anonymous inner classes and local inner classes copy local variables, which cannot be changed after initialization. Here is a case:

```java
public class OutterClass {
    private String userName;

    public void test(){
        // Here i is initialized to 1 and cannot be modified further
        int i = 1;
        class Inner{
            public void printName(){
                System.out.println(userName);
                System.out.println(i);
            }
        }
    }
}
```

After decompilation:

```java
// The result of decompiling Inner using the javap command
// i is copied into the inner class and is final
class OutterClass$1Inner {
  final int val$i;
  final OutterClass this$0;
  OutterClass$1Inner();
  public void printName();
}
```

### Conditional Compilation

Typically, every line of code in a program is subject to compilation. However, sometimes for optimization purposes, we may want only a part of the code to be compiled. At this point, we need to add conditions in the program to instruct the compiler to compile only the code that meets specific conditions, discarding the others. This is known as conditional compilation.

In C or C++, preprocessor directives can achieve conditional compilation. In fact, Java can also implement conditional compilation. Let's look at a code snippet:

```java
public class ConditionalCompilation {
    public static void main(String[] args) {
        final boolean DEBUG = true;
        if(DEBUG) {
            System.out.println("Hello, DEBUG!");
        }

        final boolean ONLINE = false;

        if(ONLINE){
            System.out.println("Hello, ONLINE!");
        }
    }
}
```

The decompiled code is as follows:

```java
public class ConditionalCompilation
{

    public ConditionalCompilation()
    {
    }

    public static void main(String args[])
    {
        boolean DEBUG = true;
        System.out.println("Hello, DEBUG!");
        boolean ONLINE = false;
    }
}
```

Firstly, we notice that the decompiled code does not contain `System.out.println("Hello, ONLINE!");`. This is an example of conditional compilation; when `if(ONLINE)` is false, the compiler does not compile the code within it.

Thus, **the conditional compilation in Java is achieved by implementing an if statement whose condition is a constant. The principle is also syntactic sugar for Java syntax. Depending on the truth of the if condition, the compiler directly removes the code block associated with false branches. Conditionally compiled code must be placed within the method body and cannot be applied at the structural level of the entire Java class or class attributes, which is indeed more limited compared to conditionally compiling in C/C++. The Java language did not incorporate conditional compilation at its inception, yet this limitation is better than having no such feature.**

### Assertions

In Java, the `assert` keyword was introduced in JAVA SE 1.4. To prevent errors caused by legacy Java code that used the `assert` keyword, assertions are not checked by default during execution (at this time, all assertion statements will be ignored!). To enable assertion checking, the `-enableassertions` or `-ea` switch is required.

Here’s a code snippet containing assertions:

```java
public class AssertTest {
    public static void main(String args[]) {
        int a = 1;
        int b = 1;
        assert a == b;
        System.out.println("Public account: Hollis");
        assert a != b : "Hollis";
        System.out.println("Blog: www.hollischuang.com");
    }
}
```

The decompiled code looks like this:

```java
public class AssertTest {
   public AssertTest()
    {
    }
    public static void main(String args[])
{
    int a = 1;
    int b = 1;
    if(!$assertionsDisabled && a != b)
        throw new AssertionError();
    System.out.println("\u516C\u4F17\u53F7\uFF1AHollis");
    if(!$assertionsDisabled && a == b)
    {
        throw new AssertionError("Hollis");
    } else
    {
        System.out.println("\u535A\u5BA2\uFF1Awww.hollischuang.com");
        return;
    }
}

static final boolean $assertionsDisabled = !com/hollis/suguar/AssertTest.desiredAssertionStatus();

}
```

It is evident that the decompiled code is considerably more complex than our own code. Thus, using the assert syntactic sugar saves us a lot of code. **The underlying implementation of assertions is really just an if statement. If the assertion evaluates to true, nothing happens, and the program continues executing. If the assertion results in false, an AssertError is thrown, halting the program. The `-enableassertions` switch sets the value for `$assertionsDisabled`.**

### Numeric Literals

In Java 7, numeric literals, both integers and floating-point numbers, allow the insertion of any number of underscores between digits. These underscores do not affect the value of the literal; their purpose is to enhance readability.

For example:

```java
public class Test {
    public static void main(String... args) {
        int i = 10_000;
        System.out.println(i);
    }
}
```

After decompilation:

```java
public class Test
{
  public static void main(String[] args)
  {
    int i = 10000;
    System.out.println(i);
  }
}
```

After decompilation, the underscores are removed. This means that **the compiler does not recognize the underscores in numeric literals and removes them during compilation.**

### For-each

The enhanced for loop (`for-each`) should be quite familiar to everyone; it is commonly used in daily development and requires much less code than a traditional for loop. What is the underlying implementation of this syntactic sugar?

```java
public static void main(String... args) {
    String[] strs = {"Hollis", "Public account: Hollis", "Blog: www.hollischuang.com"};
    for (String s : strs) {
        System.out.println(s);
    }
    List<String> strList = ImmutableList.of("Hollis", "Public account: Hollis", "Blog: www.hollischuang.com");
    for (String s : strList) {
        System.out.println(s);
    }
}
```

The decompiled code is as follows:

```java
public static transient void main(String args[])
{
    String strs[] = {
        "Hollis", "\u516C\u4F17\u53F7\uFF1AHollis", "\u535A\u5BA2\uFF1Awww.hollischuang.com"
    };
    String args1[] = strs;
    int i = args1.length;
    for(int j = 0; j < i; j++)
    {
        String s = args1[j];
        System.out.println(s);
    }

    List strList = ImmutableList.of("Hollis", "\u516C\u4F17\u53F7\uFF1AHollis", "\u535A\u5BA2\uFF1Awww.hollischuang.com");
    String s;
    for(Iterator iterator = strList.iterator(); iterator.hasNext(); System.out.println(s))
        s = (String)iterator.next();

}
```

The code is straightforward; **the implementation of `for-each` is essentially a standard for loop and an iterator.**

### Try-with-Resources

In Java, for costly resources such as file operations, IO streams, and database connections, it is crucial to close them promptly using the close method, to avoid memory leaks and other issues.

A common way to close resources is within a `finally` block, such as:

```java
public static void main(String[] args) {
    BufferedReader br = null;
    try {
        String line;
        br = new BufferedReader(new FileReader("d:\\hollischuang.xml"));
        while ((line = br.readLine()) != null) {
            System.out.println(line);
        }
    } catch (IOException e) {
        // handle exception
    } finally {
        try {
            if (br != null) {
                br.close();
            }
        } catch (IOException ex) {
            // handle exception
        }
    }
}
```

Starting from Java 7, a better way to close resources was introduced using the `try-with-resources` statement, rewriting the previous code as follows:

```java
public static void main(String... args) {
    try (BufferedReader br = new BufferedReader(new FileReader("d:\\hollischuang.xml"))) {
        String line;
        while ((line = br.readLine()) != null) {
            System.out.println(line);
        }
    } catch (IOException e) {
        // handle exception
    }
}
```

This is a significant improvement; while I typically used `IOUtils` for closing streams and avoided writing extensive code in `finally` blocks, this new form of syntactic sugar appears to be much more elegant. Let’s take a look at its underlying implementation:

```java
public static transient void main(String args[])
    {
        BufferedReader br;
        Throwable throwable;
        br = new BufferedReader(new FileReader("d:\\hollischuang.xml"));
        throwable = null;
        String line;
        try
        {
            while((line = br.readLine()) != null)
                System.out.println(line);
        }
        catch(Throwable throwable2)
        {
            throwable = throwable2;
            throw throwable2;
        }
        if(br != null)
            if(throwable != null)
                try
                {
                    br.close();
                }
                catch(Throwable throwable1)
                {
                    throwable.addSuppressed(throwable1);
                }
            else
                br.close();
            break MISSING_BLOCK_LABEL_113;
            Exception exception;
            exception;
            if(br != null)
                if(throwable != null)
                    try
                    {
                        br.close();
                    }
                    catch(Throwable throwable3)
                      {
                        throwable.addSuppressed(throwable3);
                    }
                else
                    br.close();
        throw exception;
        IOException ioexception;
        ioexception;
    }
}
```

**The underlying principle is quite simple; the resource closing operations that we omitted are automatically handled by the compiler. This reinforces the idea that syntactic sugar serves to facilitate programmers, yet it ultimately converts to a language understood by the compiler.**

### Lambda Expressions

Regarding lambda expressions, some may question their classification as syntactic sugar, with some online claiming they are not. I would like to clarify this assertion: **Lambda expressions are not simply syntactic sugar for anonymous inner classes; however, they are a form of syntactic sugar. Their implementation heavily relies on several JVM-level lambda-related APIs.**

Let’s first examine a simple lambda expression that iterates over a list:

```java
public static void main(String... args) {
    List<String> strList = ImmutableList.of("Hollis", "Public account: Hollis", "Blog: www.hollischuang.com");

    strList.forEach( s -> { System.out.println(s); } );
}
```

Why is it said that this is not just syntactic sugar over inner classes? As discussed earlier, an inner class compiling results in two class files, whereas a class containing a lambda expression compiles into only a single file.

The decompiled code is as follows:

```java
public static /* varargs */ void main(String ... args) {
    ImmutableList strList = ImmutableList.of((Object)"Hollis", (Object)"\u516c\u4f17\u53f7\uff1aHollis", (Object)"\u535a\u5ba2\uff1awww.hollischuang.com");
    strList.forEach((Consumer<String>)LambdaMetafactory.metafactory(null, null, null, (Ljava/lang/Object;)V, lambda$main$0(java.lang.String ), (Ljava/lang/String;)V)());
}

private static /* synthetic */ void lambda$main$0(String s) {
    System.out.println(s);
}
```

Here, within the `forEach` method, we notice that it calls `java.lang.invoke.LambdaMetafactory#metafactory`, where the fourth parameter `implMethod` specifies the method implementation. It is clear that `lambda$main$0` is invoked for the output.

Now, let’s look at a slightly more complicated example, where we filter the List first and then output the results:

```java
public static void main(String... args) {
    List<String> strList = ImmutableList.of("Hollis", "Public account: Hollis", "Blog: www.hollischuang.com");

    List HollisList = strList.stream().filter(string -> string.contains("Hollis")).collect(Collectors.toList());

    HollisList.forEach( s -> { System.out.println(s); } );
}
```

The decompiled code looks like this:

```java
public static /* varargs */ void main(String ... args) {
    ImmutableList strList = ImmutableList.of((Object)"Hollis", (Object)"\u516c\u4f17\u53F7\uff1aHollis", (Object)"\u535a\u5ba2\uff1awww.hollischuang.com");
    List<Object> HollisList = strList.stream().filter((Predicate<String>)LambdaMetafactory.metafactory(null, null, null, (Ljava/lang/Object;)Z, lambda$main$0(java.lang.String ), (Ljava/lang/String;)Z)()).collect(Collectors.toList());
    HollisList.forEach((Consumer<Object>)LambdaMetafactory.metafactory(null, null, null, (Ljava/lang/Object;)V, lambda$main$1(java.lang.Object ), (Ljava/lang/Object;)V)());
}

private static /* synthetic */ void lambda$main$1(Object s) {
    System.out.println(s);
}

private static /* synthetic */ boolean lambda$main$0(String string) {
    return string.contains("Hollis");
}
```

The two lambda expressions call two different methods, `lambda$main$1` and `lambda$main$0`.

**The implementation of lambda expressions relies on some low-level APIs, and during the compilation phase, the compiler will desugar the lambda expressions, converting them into calls to these internal APIs.**

## Potential Pitfalls

### Generics

**1. When Generics Encounter Overloads**

```java
public class GenericTypes {

    public static void method(List<String> list) {
        System.out.println("invoke method(List<String> list)");
    }

    public static void method(List<Integer> list) {
        System.out.println("invoke method(List<Integer> list)");
    }
}
```

The code above has two overloaded functions, differentiated by their parameter types, one is `List<String>` and the other is `List<Integer>`, but this code will not compile. This is because, as we discussed earlier, the types `List<Integer>` and `List<String>` are both erased to the same raw type `List`. This erasure results in the method signatures becoming identical.

**2. When Generics Encounter Catch Statements**

Generic type parameters cannot be used in catch statements in Java exception handling. This is because exception handling is performed by the JVM at runtime. Since type information is erased, the JVM cannot distinguish between two exception types, `MyException<String>` and `MyException<Integer>`.

**3. When Generics Include Static Variables**

```java
public class StaticTest {
    public static void main(String[] args) {
        GT<Integer> gti = new GT<Integer>();
        gti.var = 1;
        GT<String> gts = new GT<String>();
        gts.var = 2;
        System.out.println(gti.var);
    }
}
class GT<T> {
    public static int var = 0;
    public void nothing(T x) {}
}
```

The output of the above code will be: 2!

Some might mistakenly think that generic classes are different classes with separate bytecodes, but actually, due to type erasure, all instances of the generic class share the same bytecode. The static variable for this generic class is therefore shared. The `GT<Integer>.var` and `GT<String>.var` refer to the same variable.

### Auto-Boxing and Unboxing

**Object Equality Comparison**

```java
public static void main(String[] args) {
    Integer a = 1000;
    Integer b = 1000;
    Integer c = 100;
    Integer d = 100;
    System.out.println("a == b is " + (a == b));
    System.out.println("c == d is " + (c == d));
}
```

The output will be:

```plain
a == b is false
c == d is true
```

In Java 5, a new feature was introduced for `Integer` operations to save memory and enhance performance. Integer objects leverage the same reference for caching and reuse.

> This applies to integer values in the range of -128 to +127.
>
> It is only applicable for auto-boxing. Using constructors to create objects does not apply.

### Enhanced For Loop

```java
for (Student stu : students) {
    if (stu.getId() == 2)
        students.remove(stu);
}
```

This will throw a `ConcurrentModificationException`.

The Iterator works in an independent thread and possesses a mutex lock. Once an iterator is created, it builds a singly-linked list index pointing to the original object. If the original object’s quantity changes, the contents of this index will not be synchronized, therefore when the index pointer moves forward, it fails to find the object to iterate, causing the `java.util.ConcurrentModificationException` to be immediately thrown following the fail-fast principle.

Thus, `Iterator` is not allowed to modify objects during its operation. However, you can use the `remove()` method of `Iterator` itself to delete objects. The `Iterator.remove()` method will maintain the index's consistency while removing the current iteration object.

## Conclusion

This article introduced 12 commonly used syntactic sugars in Java. Syntactic sugar is simply a syntax designed to facilitate development for programmers. However, this syntax is only recognized by developers. To be executed, it needs to be desugared, meaning it is converted to syntax that the JVM understands. After we desugar these syntactic sugars, we realize that many of the convenient syntaxes we use daily are fundamentally composed of simpler syntactic structures.

With these syntactic sugars, we can significantly enhance efficiency in our daily development, but we must also be careful to avoid their overuse. It’s best to understand the principles before usage to prevent pitfalls.

<!-- @include: @article-footer.snippet.md -->
