---
title: Detailed Explanation of Basic Concepts of Authentication and Authorization
category: System Design
tag:
  - Security
---

## What is the difference between Authentication and Authorization?

This is a question that most people tend to confuse. First, let's understand the pronunciation of these two terms; many people mix up their pronunciations, so I suggest you look up how to pronounce these two words and what their specific meanings are.

To put it simply:

- **Authentication:** Who you are.
- **Authorization:** What you are allowed to do.

A slightly more formal (and verbose) explanation is:

- **Authentication** is the credential that verifies your identity (e.g., username/user ID and password). Through this credential, the system knows that you are you, meaning the system recognizes you as a user. Therefore, Authentication is referred to as identity/user verification.
- **Authorization** occurs after Authentication. The meaning of authorization is straightforward; it mainly governs our access rights to the system. For example, certain specific resources can only be accessed by users with specific permissions, such as admin, and certain operations on system resources, like deleting, adding, or updating, can only be performed by specific users.

Authentication:

![Authentication Login](https://oss.javaguide.cn/github/javaguide/system-design/security/authentication-login.png)

Authorization:

![No Permission](https://oss.javaguide.cn/github/javaguide/system-design/security/20210604161032412.png)

These two are generally used together in our systems to protect the security of our systems.

## Are you familiar with the RBAC model?

The most commonly used access control model for system permission control is the **RBAC model**.

**What is RBAC?** RBAC stands for Role-Based Access Control. It is a way of associating permissions with roles, which in turn are associated with user authorizations.

In simple terms: a user can have several roles, and each role can be assigned several permissions, thus forming a "user-role-permission" authorization model. In this model, there is a many-to-many relationship between users and roles, and roles and permissions.

![RBAC Permission Model Diagram](https://oss.javaguide.cn/github/javaguide/system-design/security/design-of-authority-system/rbac.png)

In the RBAC permission model, permissions are associated with roles, and users obtain these roles' permissions by becoming members of specific roles, which greatly simplifies permission management.

To implement the RBAC permission model, a common database table design is as follows (a total of 5 tables, with 2 tables establishing the relationship between users):

![](https://oss.javaguide.cn/2020-11/%E6%95%B0%E6%8D%AE%E5%BA%93%E8%AE%BE%E8%AE%A1-%E6%9D%83%E9%99%90.png)

Through this permission model, we can create different roles and assign different permission scopes (menus) to different roles.

![](https://oss.javaguide.cn/github/javaguide/books%E6%9D%83%E9%99%90%E7%AE%A1%E7%90%86%E6%A8%A1%E5%9D%97.png)

Generally speaking, if the system has strict requirements for permission control, it usually opts to use the RBAC model for permission control.

## What is a Cookie? What is the role of a Cookie?

![](https://oss.javaguide.cn/github/javaguide/system-design/security/cookie-sessionId.png)

`Cookie` and `Session` are both session methods used to track browser user identities, but their application scenarios are different.

Wikipedia defines `Cookie` as follows:

> `Cookies` are data stored on the user's local terminal by certain websites to identify user identities (usually encrypted).

In simple terms: **`Cookie` is stored on the client side and is generally used to save user information**.

Here are some application cases of `Cookie`:

1. We save the information of users who have logged in through `Cookie`, so that when they visit the website next time, the page can automatically fill in some basic information for them. In addition, `Cookie` can also save user preferences, themes, and other settings.
1. Use `Cookie` to save `SessionId` or `Token`, and include the `Cookie` when sending requests to the backend, so the backend can retrieve the `Session` or `Token`. This allows the system to track the user's current state since the HTTP protocol is stateless.
1. `Cookie` can also be used to record and analyze user behavior. For example, when you are shopping online, because the HTTP protocol is stateless, if the server wants to know how long you stayed on a certain page or which products you viewed, a common implementation is to store this information in `Cookie`.
1. ……

## How to use Cookies in a project?

I will take a Spring Boot project as
