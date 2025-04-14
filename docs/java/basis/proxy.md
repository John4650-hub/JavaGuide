---
title: Detailed Explanation of Java Proxy Pattern
category: Java
tag:
  - Java Basics
---

## 1. Proxy Pattern

The proxy pattern is a design pattern that is relatively easy to understand. Simply put, **we use a proxy object to replace the access to the real object, which allows us to provide additional functionality and extend the real object's capabilities without modifying it.**

**The main purpose of the proxy pattern is to extend the functionality of the target object. For example, you can add some custom operations before and after a certain method of the target object is executed.**

For instance: a bride asks her aunt to handle the groom's inquiries on her behalf. The inquiries that the bride receives have all been filtered by the aunt. The aunt can be viewed as the proxy object, whose role (method) is to receive and respond to the groom's questions.

![Understanding the Proxy Design Pattern | by Mithun Sasidharan | Medium](https://oss.javaguide.cn/2020-8/1*DjWCgTFm-xqbhbNQVsaWQw.png)

<p style="text-align:right;font-size:13px;color:gray">https://medium.com/@mithunsasidharan/understanding-the-proxy-design-pattern-5e63fe38052a</p>

There are two implementations of the proxy pattern: static proxy and dynamic proxy. Let's first look at the implementation of static proxy.

## 2. Static Proxy

**In static proxy, the enhancement for each method of the target object is done manually (_code demonstration will follow_), which is very inflexible (_for example, if the interface adds a new method, both the target object and the proxy object need to be modified_) and troublesome (_a separate proxy class needs to be written for each target class_).** The actual application of static proxies is very rare; you hardly see static proxies used in daily development.

The above perspective is from the implementation and application angle of static proxy. From the JVM perspective, **the static proxy generates the interface, implementation class, and proxy class into actual class files at compile time.**

Steps to implement static proxy:

1. Define an interface and its implementation class.
1. Create a proxy class that also implements this interface.
1. Inject the target object into the proxy class, and then call the corresponding method of the target class within the proxy class's corresponding method. This way, we can shield the access to the target object through the proxy class and perform some desired operations before and after the execution of the target method.

Let's demonstrate this with code!

**1. Define the interface for sending messages**

```java
public interface SmsService {
    String send(String message);
}
```

**2. Implement the interface for sending messages**

```java
public class SmsServiceImpl implements SmsService {
    public String send(String message) {
        System.out.println("send message:" + message);
        return message;
    }
}
```

**3. Create a proxy class that also implements the interface for sending messages**

```java
public class SmsProxy implements SmsService {

    private final SmsService smsService;

    public SmsProxy(SmsService smsService) {
        this.smsService = smsService;
    }

    @Override
    public String send(String message) {
        // Before calling the method, we can add our own operations
        System.out.println("before method send()");
        smsService.send(message);
        // After calling the method, we can also add our own operations
        System.out.println("after method send()");
        return null;
    }
}
```

**4. Actual usage**

```java
public class Main {
    public static void main(String[] args) {
        SmsService smsService = new SmsServiceImpl();
        SmsProxy smsProxy = new SmsProxy(smsService);
        smsProxy.send("java");
    }
}
```

After running the above code, the console prints:

```bash
before method send()
send message:java
after method send()
```

From the output, we can see that we have added functionality to the `send()` method of `SmsServiceImpl`.

## 3. Dynamic Proxy

Compared to static proxy, dynamic proxy is more flexible. We do not need to create a separate proxy class for each target class, nor do we need to implement an interface; we can directly proxy the implementation class (_CGLIB dynamic proxy mechanism_).

**From the JVM perspective, dynamic proxy dynamically generates bytecode for classes at runtime and loads it into the JVM.**

When it comes to dynamic proxy, Spring AOP and RPC frameworks are two notable examples that rely on dynamic proxy.

**Dynamic proxy is used relatively less in our daily development, but it is an essential technology in frameworks. Understanding dynamic proxies is very helpful for us to comprehend and learn the principles of various frameworks.**

In Java, there are many ways to implement dynamic proxies, such as **JDK Dynamic Proxy** and **CGLIB Dynamic Proxy**.

[guide-rpc-framework](https://github.com/Snailclimb/guide-rpc-framework) uses JDK dynamic proxy; let's first take a look at how to use JDK dynamic proxy.

Additionally, although [guide-rpc-framework](https://github.com/Snailclimb/guide-rpc-framework) does not utilize **CGLIB Dynamic Proxy**, we will briefly introduce its usage and compare it with **JDK Dynamic Proxy**.

### 3.1. JDK Dynamic Proxy Mechanism

#### 3.1.1. Introduction

**In Java's dynamic proxy mechanism, the `InvocationHandler` interface and the `Proxy` class are the core components.**

The most frequently used method in the `Proxy` class is: `newProxyInstance()`, which is primarily used to generate a proxy object.

```java
    public static Object newProxyInstance(ClassLoader loader,
                                          Class<?>[] interfaces,
                                          InvocationHandler h)
        throws IllegalArgumentException
    {
        ......
    }
```

This method has three parameters:

1. **loader**: The class loader used to load the proxy object.
1. **interfaces**: The interfaces implemented by the target class.
1. **h**: An object that implements the `InvocationHandler` interface.

To implement dynamic proxy, you must also implement `InvocationHandler` to customize the handling logic. When our dynamic proxy object calls a method, the call will be forwarded to the `invoke` method of the class implementing the `InvocationHandler` interface.

```java
public interface InvocationHandler {

    /**
     * This method is invoked when you call a method on the proxy object.
     */
    public Object invoke(Object proxy, Method method, Object[] args)
        throws Throwable;
}
```

The `invoke()` method has three parameters:

1. **proxy**: The dynamically generated proxy class.
1. **method**: The method corresponding to the method called on the proxy class object.
1. **args**: The parameters for the current method.

In other words, **the proxy object created by the `Proxy` class's `newProxyInstance()` will actually invoke the `invoke()` method of the class implementing the `InvocationHandler` interface when calling a method.** You can customize the handling logic in the `invoke()` method, such as performing tasks before and after method execution.

#### 3.1.2. Steps to Use JDK Dynamic Proxy

1. Define an interface and its implementation class.
1. Customize the `InvocationHandler` and override the `invoke` method, where we will call the actual method (the method of the proxied class) and customize some handling logic.
1. Create a proxy object using the `Proxy.newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)` method.

#### 3.1.3. Code Example

This might seem a bit abstract and hard to understand, so let's use an example to illustrate!

**1. Define the interface for sending messages**

```java
public interface SmsService {
    String send(String message);
}
```

**2. Implement the interface for sending messages**

```java
public class SmsServiceImpl implements SmsService {
    public String send(String message) {
        System.out.println("send message:" + message);
        return message;
    }
}
```

**3. Define a JDK dynamic proxy class**

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

/**
 * @author shuang.kou
 * @createTime 2020年05月11日 11:23:00
 */
public class DebugInvocationHandler implements InvocationHandler {
    /**
     * The real object in the proxy class.
     */
    private final Object target;

    public DebugInvocationHandler(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws InvocationTargetException, IllegalAccessException {
        // Before calling the method, we can add our own operations
        System.out.println("before method " + method.getName());
        Object result = method.invoke(target, args);
        // After calling the method, we can also add our own operations
        System.out.println("after method " + method.getName());
        return result;
    }
}
```

The `invoke()` method: When our dynamic proxy object calls the actual method, it ultimately calls the `invoke()` method, which then calls the original method of the proxied object.

**4. Factory class to get the proxy object**

```java
public class JdkProxyFactory {
    public static Object getProxy(Object target) {
        return Proxy.newProxyInstance(
                target.getClass().getClassLoader(), // The class loader of the target class
                target.getClass().getInterfaces(),  // Interfaces that the proxy needs to implement, can specify multiple
                new DebugInvocationHandler(target)   // Custom InvocationHandler corresponding to the proxy object
        );
    }
}
```

`getProxy()`: Primarily obtains the proxy object of a certain class through the `Proxy.newProxyInstance()` method.

**5. Actual usage**

```java
SmsService smsService = (SmsService) JdkProxyFactory.getProxy(new SmsServiceImpl());
smsService.send("java");
```

After running the above code, the console prints:

```plain
before method send
send message:java
after method send
```

### 3.2. CGLIB Dynamic Proxy Mechanism

#### 3.2.1. Introduction

**The most critical issue with JDK dynamic proxy is that it can only proxy classes that implement interfaces.**

**To resolve this issue, we can use the CGLIB dynamic proxy mechanism.**

[CGLIB](https://github.com/cglib/cglib) (_Code Generation Library_) is a bytecode generation library based on [ASM](http://www.baeldung.com/java-asm). It allows us to modify and dynamically generate bytecode at runtime. CGLIB implements proxying through inheritance. Many well-known open-source frameworks utilize [CGLIB](https://github.com/cglib/cglib), such as Spring's AOP module: if the target object implements an interface, JDK dynamic proxy is used by default; otherwise, CGLIB dynamic proxy is used.

**In the CGLIB dynamic proxy mechanism, the `MethodInterceptor` interface and the `Enhancer` class are the core components.**

You need to customize `MethodInterceptor` and override the `intercept` method, which is used to intercept and enhance the proxied class's method.

```java
public interface MethodInterceptor extends Callback {
    // Intercept methods in the proxied class
    public Object intercept(Object obj, java.lang.reflect.Method method, Object[] args, MethodProxy proxy) throws Throwable;
}
```

1. **obj**: The object being proxied (the object to be enhanced).
1. **method**: The intercepted method (the method to be enhanced).
1. **args**: Method parameters.
1. **proxy**: Used to call the original method.

You can dynamically obtain the proxied class through the `Enhancer` class. When the proxy class calls a method, the actual call is made to the `intercept` method in `MethodInterceptor`.

#### 3.2.2. Steps to Use CGLIB Dynamic Proxy

1. Define a class.
1. Customize `MethodInterceptor` and override the `intercept` method, similar to the `invoke` method in JDK dynamic proxy.
1. Create a proxy class using the `Enhancer` class's `create()` method.

#### 3.2.3. Code Example

Unlike JDK dynamic proxy, CGLIB dynamic proxy doesn't require additional dependencies. [CGLIB](https://github.com/cglib/cglib) (_Code Generation Library_) is an open-source project; if you wish to use it, you need to manually add the relevant dependencies.

```xml
<dependency>
  <groupId>cglib</groupId>
  <artifactId>cglib</artifactId>
  <version>3.3.0</version>
</dependency>
```

**1. Implement a class for sending messages using Aliyun**

```java
package github.javaguide.dynamicProxy.cglibDynamicProxy;

public class AliSmsService {
    public String send(String message) {
        System.out.println("send message:" + message);
        return message;
    }
}
```

**2. Customize `MethodInterceptor` (method interceptor)**

```java
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

/**
 * Custom MethodInterceptor
 */
public class DebugMethodInterceptor implements MethodInterceptor {

    /**
     * @param o           The object being proxied (the object to be enhanced).
     * @param method      The intercepted method (the method to be enhanced).
     * @param args        Method parameters.
     * @param methodProxy Used to call the original method.
     */
    @Override
    public Object intercept(Object o, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
        // Before calling the method, we can add our own operations
        System.out.println("before method " + method.getName());
        Object object = methodProxy.invokeSuper(o, args);
        // After calling the method, we can also add our own operations
        System.out.println("after method " + method.getName());
        return object;
    }
}
```

**3. Obtain the proxy class**

```java
import net.sf.cglib.proxy.Enhancer;

public class CglibProxyFactory {

    public static Object getProxy(Class<?> clazz) {
        // Create dynamic proxy enhancement class
        Enhancer enhancer = new Enhancer();
        // Set the class loader
        enhancer.setClassLoader(clazz.getClassLoader());
        // Set the proxied class
        enhancer.setSuperclass(clazz);
        // Set the method interceptor
        enhancer.setCallback(new DebugMethodInterceptor());
        // Create the proxy class
        return enhancer.create();
    }
}
```

**4. Actual usage**

```java
AliSmsService aliSmsService = (AliSmsService) CglibProxyFactory.getProxy(AliSmsService.class);
aliSmsService.send("java");
```

After running the above code, the console prints:

```bash
before method send
send message:java
after method send
```

### 3.3. Comparison Between JDK Dynamic Proxy and CGLIB Dynamic Proxy

1. **JDK dynamic proxy can only proxy classes that implement interfaces or directly proxy interfaces, while CGLIB can proxy classes that do not implement any interfaces.** Additionally, CGLIB dynamic proxy intercepts method calls by generating a subclass of the proxied class, so it cannot proxy methods or classes declared as final.
1. In terms of efficiency, JDK dynamic proxy is generally more efficient. As JDK versions improve, this advantage becomes even more pronounced.

## 4. Comparison Between Static Proxy and Dynamic Proxy

1. **Flexibility**: Dynamic proxy is more flexible; there is no need to implement an interface, and it can directly proxy the implementation class without creating a separate proxy class for each target class. Additionally, in static proxy, if a new method is added to the interface, modifications need to be made to both the target object and the proxy object, which is cumbersome.
1. **JVM Perspective**: Static proxy generates the interface, implementation class, and proxy class into actual class files at compile time, while dynamic proxy dynamically generates class bytecode at runtime and loads it into the JVM.

## 5. Summary

This article mainly introduces two implementations of the proxy pattern: static proxy and dynamic proxy. It covers the practical applications of static and dynamic proxies, the differences between them, and the distinctions between JDK and CGLIB dynamic proxies.

All the source code involved in this article can be found here: [https://github.com/Snailclimb/guide-rpc-framework-learning/tree/master/src/main/java/github/javaguide/proxy](https://github.com/Snailclimb/guide-rpc-framework-learning/tree/master/src/main/java/github/javaguide/proxy).

<!-- @include: @article-footer.snippet.md -->
