I don't...
Title: Java Proxy Model Details
Category:
Tag:

- Java Foundation.
  I don't...

# 1. Proxy Model

The proxy model is a more understandable design model. Simply put, **we use proxy objects to replace access to real objects, so that additional functionality can be provided without modifying the original object and expanding the function of the object.**

**The main function of the proxy model is to expand the function of the target object, for example, by adding some self-defined operations before and after the implementation of a method for the target object.**

By way of example, the bride has found her aunt to deal with the groom's questions in her place, and the bride's questions have been filtered by the aunt. The aunt here can be considered as the agent for you, and the agent's behavior (method) is to receive and answer the groom's questions.

![Proxy Model](https://oss.javaguide.cn/2020-8/1*DjWCgTFm-xqbhbbnQVsaWQw.png)

<p style="text-align:right; font-size:13px; color:gray">https://mediam.com/@mihunsaidharan/understand-the-proxy-design-pattern-5e63fe38052a</p>

The proxy model has both static and dynamic agents, and let's first look at the static agent model.

# 2. Static Agents

**In static agents, each enhancement of the target object's method is a manual (specific presentation code), very inflexible (such as changes to the target and proxy if the interface is added) and trouble (requires a separate proxy category for each target category).** The actual applications are very, very few, and the use of static agents is almost invisible in day-to-day development.

We're static agents from the point of view of realization and application, and from the point of view of JVM, **static agents translate interfaces, realizations, and proxy categories into a real class file.**

Static agent realization steps:

1. Definition of an interface and its realization categories;
1. Create a proxy category to equally achieve this interface;
1. Inject the target object into the proxy category and then use the corresponding method in the proxy category. In that way, we can block access to the target audience through proxy categories and can do what we want before and after the implementation of the target approach.

Here's the code!

**1. Defined Interface for Texting**

```Java
This post is part of our special coverage Global Voices 2011.
Bring send;
♪ I'm sorry ♪
```

**2. Reaching the Interface for Texting**

```Java
public class Sms {
    Spring message;
    System.out.println("I'm sorry.");
    ♪ I'm sorry ♪
}
```

**3. Create an Interface for Proxy Category and Equally for Texting**

```Java
public class SmsProxy {
    private Sms smsService;

    public SmsProxy(Sms smsService) {
        this.smsService = smsService;
    }

    @Override
    public void send(String message) {
        // Before calling, we can add our own operation
        System.out.println("before method send()");
        smsService.send(message);
        // After the call method, we can also add our own operations
        System.out.println("after method send()");
    }
}
```

**4. Actual Use**

```Java
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
Send message: java
after method send()
```

As can be seen from the output, we have added the `send()` method of `SmsServiceImpl`.

# 3. Dynamic Agents

Dynamic agents are more flexible than static agents. We do not need to create a single proxy category for each target category, and we do not need to have an interface, which we can do directly (CGLIB Dynamic Agent Mechanism).

**Dynamic agents are dynamically generating bytes from a JVM perspective and loading them into JVM.**

As far as dynamic agents are concerned, Spring AOP and the RPC framework should have two to mention, and their realization depends on dynamic agents.

**Dynamic agents are used relatively little in our day-to-day development, but in the framework, they are almost a necessary technology. Learning about dynamic agents has also been very helpful in understanding and learning about the principles of frameworks.**

In the case of Java, there are many ways in which dynamic agents can be achieved, such as **JDK Dynamic Agent**, **CGLIB Dynamic Agent**, and so on.

[guide-rpc-framework](https://github.com/Snailclimb/guide-rpc-framework) uses JDK dynamic agents, and let's look at the use of JDK dynamic agents.

In addition, although \[guide-rpc
