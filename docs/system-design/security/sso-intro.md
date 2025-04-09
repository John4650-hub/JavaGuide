I don't...
Title: SSO Single Point Login Details
Category: System Design
Tag:

- Clear.
  I don't...

It is authorized to be reproduced from: <https://ken.io/note/sso-design-implement> by: ken.io

# SSO Introduction

# What's SSO?

SSO's English full name is Single Sign-On, a single point login. SSO is a multi-application system in which users can access all mutually trusted applications only once.

For example, you have access to all the following sites after you have access to the Internet Earning Centre (<https://reg.163.com/>).

- Live webcast [https://v.163.com](https://v.163.com/)
- Online blog [https://blog163.com](https://blog163.com/)
- Web-based florist [https://love.163.com](https://love.163.com/)
- e-Cola [https://www.kaola.com](https://www.kaola.com/)
- Lofter [http://www.lofter.com](http://www.lofter.com/)

What good is SSO?

1. **User angle**: Users are able to log in multiple times without having to remember multiple usernames and passwords.
1. **System Administrator angle**: Only one single account center can be maintained by the administrator, which is convenient.
1. **New system development perspective**: New system development needs to be directly linked to a unified account center to simplify the development process and save time.

# SSO Design and Realization

This article is also about how to design and achieve an SSO system.

The following are the core functions to be achieved:

- Single-point login.
- One point out.
- Support cross-domain unit login.
- Support cross-domain logout.

## Core Application and Dependence

![Core Application and Dependence](https://oss.javaguide.cn/github/javaguide/system-design/security/sso/sso-system.png-kblb.png)

| Application/Module/Object  | Description                                            |
| -------------------------- | ------------------------------------------------------ |
| SSO Site - Login           | Provides login pages                                   |
| SSO Site - Login and Out   | Provides login and logout functionality                |
| SSO Service - Login        | Handles login requests                                 |
| SSO Service - Login Status | Provides login status check/login information query    |
| SSO Service - Logout       | Handles logout requests                                |
| Database                   | Storage of user account information                    |
| Cache                      | Storage of user login information, usually using Redis |

Storage and verification of user login status.

The common web framework uses a session ID that is stored in the browser cookie. This [ken.io](https://ken.io/) was also mentioned earlier (https://ken.io/note/session-principle-skill). It's the same idea.

Generate AuthToken to save to the client after user login is successful. If the browser saves it in a cookie. If it's an app, it's in the app's local cache. This article focuses on the SSO-based website.

Users submit AuthToken to the SSO service to check login status/take user login information when browsing pages that require login.

For the storage of login information, it is recommended that Redis be used to store login information using Redis clusters, which would ensure both high availability and linear expansion. At the same time, SSO services can be used to meet load-balanced/scalable needs.

Note

| AuthToken         | Use UUID/GUID directly. If there is a need to validate AuthToken's legitimacy, you can encrypt Username + timestamp to create legal validity after the service decryption. |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Login Information | Usually Cache UpId, Username                                                                                                                                               |

# User Login/Login Verification

**Sequence Chart of Login**

![SO System Design - Login Scheduling](https://oss.javaguide.cn/github/javaguide/system-design/security/sso/sso-login-sequence.png-kbrb.png)

According to the above figure, AuthToken is saved in a cookie after user login. Domain=test.com. The browser will set the domain to .test.com.

This way, all websites of \\.test.com will carry AuthToken to the server. Then complete the verification/user login information through the SSO service.

**Login Information Acquisition/Entry Status Verification**

![SO System Design - Access to Login Information/Registration Status Validation](https://oss.javaguide.cn/github/javaguide/system-design/security/sso/sso-logincheck-security.png-kbrb.png)

# The User Logs Out

It's very simple what users do when they log out:

1. Service-level clearance of login status in Redis.
1. Client clears stored AuthToken.

**Timescaled Out**

![SO System Design - User Logout](https://oss.javaguide.cn/github/javaguide/system-design/security/sso/sso-logout-security.png-kbrb.png)

# Cross-Domain Login, Logout

As mentioned earlier, the core idea is to store AuthToken by the client and to store login information by the server via Redis. As the client is storing AuthToken
