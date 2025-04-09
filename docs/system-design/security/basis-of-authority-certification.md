I don't...
title: Detailed concepts underlying authentication and authorization
Category: System design
Tag:

- Clear.
  I don't...

What is the difference between authentication and authorization?

This is an issue that most people would be confused about. First of all, they're pronounced differently, and many people mix their pronunciations, so I suggest you go and find out what they really mean.

Let's be simple:

\*\* Who are you?\
\*\* What authority do you have?

A little bit more formal:

- **Authentication** is the proof of your identity (e.g., username/user ID and password) that enables the system to know that you are you, that is, the system recognizes you as a user. So it's called identification/user authentication.
- **Authorization** comes after **Authentication**. As for authorization, it should be understood that it is primarily in charge of our access to the system. For example, some specific resources can only be accessed by people with specific privileges, such as admin, while others are available for system resource operations, such as deleting, adding, updating, etc.

Authentication:

![Accreditation login](https://oss.javaguide.cn/github/javaguide/system-design/security/authentication-login.png)

Authorization:

![Authorization](https://oss.javaguide.cn/github/javaguide/system-design/security/20210604161032412.png)

These two are generally used together in our system, with the aim of protecting the security of our system.

# RBAC model understood?

The most commonly used access control model for system access control is the **RBAC model**.

\*\* What is RBAC? \*\* RBAC is Role-based Access Control. This is a way to link roles with the mandates of the user at the same time.

To put it simply: a user can have several roles, and each role can be assigned a number of privileges, thus creating a "user-role-authority" authorization model. In this model, there are multiple-to-multiple relationships between users and roles, and roles and privileges.

![RBAC Permission Model Map](https://oss.javaguide.cn/github/javaguide/system-design/security/design-of-autocity-system/rbac.png)

In the RBAC Permission Model, privileges are associated with roles, which users acquire by becoming members of a specific role, which greatly simplifies the management of permissions.

In order to achieve the RBAC permission model, the common design of the database tables is as follows (a total of 5 tables, 2 user-created table connections):

![Database Design](https://oss.javaguide.cn/2020-11/%E6%95%B0%E6%8D%AE%E5%BA%93%E8%AE%A1%E5%88%B6-%E6%9D%83%E9%99%90%E8%AE%A1%E5%88%B6-%E6%9D%83%E9%99%90%E8%AE%A1%E5%88%B6.png)

Through this permission model, we can create different roles and assign different areas of permissions (menu).

![Permission Areas](https://oss.javaguide.cn/github/javaguide/books%E6%9D%93%E9%99%90%E8%AE%A1%E5%88%B6%E6%A8%A1%E5%9D%97.png)

Normally, if the system is more stringent in terms of rights control requirements, the RBAC model is chosen for rights control.

What's a Cookie?

![Cookie](https://oss.javaguide.cn/github/javaguide/system-design/security/cookie-sessionId.png)

`Cookie` and `session` are both sessions used to track the user identity of the browser, but their application is different.

Wikipedia defines `Cookie` as:

> `Cookies` are data stored on user local terminals (usually encrypted) on certain websites to identify users.

Simply put: \*\* `Cookie` is stored on the client’s side and is generally used to store user information\*\*.

The following are some of the applications of `Cookie`:

1. We keep already logged-in user information in `Cookie`, and some of the basic information is automatically filled out on the page when you visit the site next time. In addition, `Cookie` saves user preferences, themes, and other settings.
1. Use `Cookie` to save `sessionId` or `Token` to send requests to the backend with `Cookie` so that the backend can retrieve the `session` or `Token`. This will record the current status of the user because the HTTP protocol is stateless.
1. `Cookie` may also be used to record and analyze user behavior. A simple example is how you shop online; because the HTTP protocol is stateless, if the server wants to access your stopover status on a page or what kind of product you see, a common way to do this is to store it in `Cookie`.
1. ...

# How to use Cookie in the project?

I take the Spring Boot project as an
