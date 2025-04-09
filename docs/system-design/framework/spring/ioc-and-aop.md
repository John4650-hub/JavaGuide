I don't...
title: IoC & AOP Detail (Quick Response)
Category: Frame
Tag:

- Spring.
  I don't...

This article expands the interpretation of IoC & AOP from the following questions:

- What's IoC?
- What's the problem?
- The difference between IoC and DI?
- What's AOP?
- AOP, what's the problem?
- What are the AOP applications?
- AOP, why is it called a methadone programming?
- What are the ways to achieve it?

First of all, IoC & AOP is not Spring; they actually existed before Spring, but they were more theoretical. Spring did both very well at the technical level.

# IoC (Inversion of Control)

What is IoC?

IoC (Inversion of Control) is control of reverse/reverse control. It is an idea, not a technology. It describes the creation and management of objects in Java development.

For example: Existing Category A Dependency Category B

- **Traditional development**: often manually via new keywords in Category A
- **Development of IoC thought**: not creating objects by new keywords, but by helping us to exemplify objects through IoC packaging (Spring framework). We need the object to get it directly from the IoC container.

From a comparison of the two types of development: we “lost one power” (the power to create, manage, etc.) and received one benefit (without taking into account the whole range of creation, management, etc.)

**Why control the reverse?**

- **Control**: refers to the power to create objects (example, manage)
- **Invert**: control is handed over to the outside environment (IoC packaging)

[IoC Graphics](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/IoC&Aop-ioc-registration.png)

What's the problem with IoC?

The IoC idea is that there is no mutual dependence between the two parties and that the related resources are managed by third-party containers. What good is that?

1. Reduced or less dependent inter-object;
1. Resources are easily managed; for example, a single case can easily be achieved if you provide it in the Spring container.

For example, there is an operation for User that uses two layers of Service and Dao structures for development.

Without using the IoC idea, if the Service level wishes to use the Dao layer for practical realization, it is necessary to use the new keyword manually from the `User ServiceImpl` for the `User Dao` specific realization category `User DaoImpl` (not a direct new interface class).

It's perfect, it's achievable, but let's imagine the following:

A new demand was suddenly received in the development process, and another specific realization category was developed for the `IUser Dao` interface. Because the Service layer relies on `IUser Dao` for concrete realization, we need to modify the new object in `User ServiceImpl`. If only one category quoted the actual realization of `IUser Dao`, it might feel good and not hard to modify it, but if so many references were made to the actual realization of `IUser Dao`, it would be very painful to change the way `IUser Dao` was achieved.

(https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/IoC&Aop-oc-illustration-dao-service.png)

Using the IoC idea, we place control of the object (creation, management) in the IoC container, and we use it directly from the IoC container.

(https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/IoC&Aop-ioc-registration-doo.png)

# IoC and DI are different?

IoC (Inversion of Control) is a design idea or a model. This design idea is **to hand over control of objects originally created manually in the program to third parties such as IoC packaging.** For our usual Spring framework, the IoC packaging is actually a Map (key, value), and Map contains a variety of objects. However, IoC is also used in other languages and is not specific to Spring.

The most common and rational way to achieve this is by relying on injection.

Martin Fowler referred in an article to the change of the name of IoC to DI, as follows: <https://martinfowler.com/articles/injection.html>.

(https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/martin-fowler-injection.png)

IoC is probably saying that IoC is too common and unsatisfactory, and that a lot of people are confused, so it's better to use DI to name it precisely.

# Aspect Oriented Programming

There are not too many professional terms here, and the central purpose is to clarify the AOP thinking.

What's AOP?

AOP (Aspect Oriented Programming) is a face-to-face programming, and AOP is a continuation of OOP (Object-oriented programming
