I don't...
title: Design details in Spring
Category: Frame
Tag:

- Spring
  I don't...

The two questions are more common in interviews.

I've been searching online about the design model in Spring, almost the same thing, and most of it is old. So it took a few days to sum it up.

Because of my limited personal capacity, you can point out any errors in the text. In addition, the article is limited in size, and I have just read the design model and some source codes, and the main purpose of the article is to review the design model in Spring.

# Control the reverse (IoC) and rely on injection (DI)

**IoC (Inversion of Control)** is a very important concept in Spring, which is not technology, but rather a decorative design idea. The main purpose of IoC is to achieve decoupling between dependent objects (IoC packaging management objects, you can only use them), with the help of “third parties” (IoC packaging in Spring), thereby reducing the degree of convergence between codes.

**IoC is a principle, not a model, which (but not limited to) realizes the IoC principle.**

[ioc-patterns](https://oss.javaguide.cn/github/javaguide/ioc-patterns.png)

**Spring IoC packaging is like a factory, and when we need to create an object, we just need to have a configuration file/notation, without any consideration of how the object was created.** IoC packaging creates objects, connects them, configures them, and processes them from their creation throughout their life cycle until they are completely destroyed.

If there are hundreds or even thousands of Service categories in the actual project, we need to exemplify this Service, and you'll probably have to figure out the tectonic function of all Service, which could drive people crazy. If you use the IoC, you just have to be configured and quoted where you need it, which greatly increases the viability of the project and reduces the difficulty of development.

> On Spring IoC's understanding, it is very good to look at a known answer under this heading: <https://www.zhihu.com/question/23277575/answer/16698662>.

**How does control reverse mean?** Example: "Object a depends on object b, and must create it when object a needs to use object b. But when the system introduces the IoC container, the direct connection between object a and object b is lost. At this time, when an object a needs to use an object b, we can specify an IoC container to create an object b into an object." Object a The process of obtaining a dependent object b, from active to passive behavior, control reverses, which controls the origin of the reverse name.

**DI (Dependency Injection) is a design model for the control of reverses, and reliance on injections is the introduction of case variables into an object.**

# Plant design model

Spring uses the plant model to create an object by `BeanFactory` or `ApplicationContext`.

**Comparative:**

- `BeanFactory`: Delayed injection (used only when a bean is used) will take less memory and start the process faster than `ApplicationContext`.
- `ApplicationContext`: When the container is activated, all beans are created at once, regardless of their usefulness. `BeanFactory` provides only minimal infusion support and `ApplicationContext` expands `BeanFactory` to include additional functions in addition to the functions of `BeanFactory`, so that the use of `ApplicationContext` by general developers is even greater.

`ApplicationContext` for three categories of realization:

`ClassPathXmlApplicationContext`: treats context documents as a type path resource.\
`FileSystemXmlApplicationContext`: contains context definition information from XML files in the file system.\
`XmlWebApplicationContext`: Load context definition information from XML files in the Web system.

Example:

```Java
I don't know.
import org.springframework.context.support.FileSystemXmlApplicationContext;

public class App {
    public static void main(String[] args) {
        ApplicationContext context = new FileSystemXmlApplicationContext("C:/work/IOC Contractors/springframework.applicationtext/src/main/resources/bean-factory-config.xml");

        HelloApplicationContext obj = (HelloApplicationContext) context.getBean("helloApplicationContext");
        obj.getMsg();
    }
}
```

# Single design mode

In our system, there are a number of objects that we actually need, for example, thread pools, caches, dialogue boxes, registration forms, log objects, objects that act as printers, graphic cards, and so on. In fact, there can only be one example of this category, and if there are several examples, they can lead to problems such as abnormality of the procedure, overuse of resources, or the result of inconsistency.

**The benefits of using a single case**:

- For frequently used objects, the amount of time spent on creating objects can be omitted, which is a significant system cost
