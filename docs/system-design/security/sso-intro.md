---
title: Detailed Explanation of SSO (Single Sign-On)
category: System Design
tag:
  - Security
---

> This article is authorized for reproduction from: <https://ken.io/note/sso-design-implement> Author: ken.io

## Introduction to SSO

### What is SSO?

SSO stands for Single Sign-On. It allows users to log in once and gain access to all mutually trusted applications without needing to log in separately for each application.

For example, after logging into the NetEase account center (<https://reg.163.com/>), you remain logged in when visiting the following sites:

- NetEase Live [https://v.163.com](https://v.163.com/)
- NetEase Blog [https://blog.163.com](https://blog.163.com/)
- NetEase Love Field [https://love.163.com](https://love.163.com/)
- NetEase Koala [https://www.kaola.com](https://www.kaola.com/)
- NetEase Lofter [http://www.lofter.com](http://www.lofter.com/)

### What are the Benefits of SSO?

1. **User Perspective**: Users can log in once and use multiple applications without needing to remember multiple sets of usernames and passwords, making it hassle-free.
1. **System Administrator Perspective**: Administrators only need to maintain a single unified account center, which is convenient.
1. **New System Development Perspective**: When developing new systems, developers can directly connect to the unified account center, simplifying the development process and saving time.

## Design and Implementation of SSO

The purpose of this article is to explore how to design and implement an SSO system.

The following are the core functions that need to be implemented:

- Single Sign-On
- Single Sign-Out
- Support for cross-domain Single Sign-On
- Support for cross-domain Single Sign-Out

### Core Applications and Dependencies

![SSO Design](https://oss.javaguide.cn/github/javaguide/system-design/security/sso/sso-system.png-kblb.png)

| Application/Module/Object  | Description                                                |
| -------------------------- | ---------------------------------------------------------- |
| Frontend Site              | The site that requires login                               |
| SSO Site - Login           | Provides the login page                                    |
| SSO Site - Logout          | Provides the entry point for logout                        |
| SSO Service - Login        | Provides login services                                    |
| SSO Service - Login Status | Provides login status verification/user info query service |
| SSO Service - Logout       | Provides user logout services                              |
| Database                   | Stores user account information                            |
| Cache                      | Stores user login information, usually using Redis         |

### Storage and Verification of User Login Status

Common web frameworks implement sessions by generating a SessionId stored in the browser's cookie, while session content is stored in server-side memory. This approach has been mentioned in [ken.io](https://ken.io/) before in the [Session Working Principle](https://ken.io/note/session-principle-skill). The overall approach is borrowed from this idea.

After a user successfully logs in, an AuthToken is generated and given to the client for storage. If it’s a browser, it is stored in the cookie. If it’s a mobile app, it is stored in the app's local cache. This article mainly discusses SSO based on web sites.

When a user visits a page requiring login, the client submits the AuthToken to the SSO service to verify the login status and obtain user login information.

For storing login information, Redis is recommended. Using a Redis cluster to store login data can ensure high availability and linear scalability. It also allows the SSO service to meet load balancing and scalability requirements.

| Object     | Description                                                                                                                                                                                 |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AuthToken  | We can directly use UUID/GUID. If there is a need to verify the validity of the AuthToken, it can be encrypted from UserName + timestamp and validated on the server side after decryption. |
| Login Info | Typically consists of caching UserId and UserName.                                                                                                                                          |

### User Login/Login Verification

**Login Sequence Diagram**

![SSO System Design - Login Sequence Diagram](https://oss.javaguide.cn/github/javaguide/system-design/security/sso/sso-login-sequence.png-kbrb.png)

According to the above diagram, after a user logs in, the AuthToken is stored in the cookie. The domain is set to .test.com.

This way, when accessing all \*.test.com web sites, the AuthToken will be carried to the server. Then, through the SSO service, the user status will be verified, and user login information will be obtained.

**Login Information Retrieval/Login Status Verification**

![SSO System Design - Login Information Retrieval/Login Status Verification](https://oss.javaguide.cn/github/javaguide/system-design/security/sso/sso-logincheck-sequence.png-kbrb.png)

### User Logout

The steps for user logout are quite simple:

1. The server clears the login status in the cache (Redis).
1. The client clears the stored AuthToken.

**Logout Sequence Diagram**

![SSO System Design - User Logout](https://oss.javaguide.cn/github/javaguide/system-design/security/sso/sso-logout-sequence.png-kbrb.png)

### Cross-Domain Login and Logout

As mentioned earlier, the core idea is to store the AuthToken on the client side while the server stores login information in Redis. Since the client stores the AuthToken in cookies, the issue to resolve for cross-domain functionality is how to handle cookie read/write operations across domains.

The core approach to solving cross-domain issues is:

- After logging in, pass the AuthToken to non-main domain sites via callback, where the site itself stores the AuthToken in the current domain's cookie.
- After logging out, call the logout page of a non-main domain site to expire the AuthToken in the cookie via callback.

**Cross-Domain Login (Main Domain Logged In)**

![SSO System Design - Cross-Domain Login (Main Domain Logged In)](https://oss.javaguide.cn/github/javaguide/system-design/security/sso/sso-crossdomain-login-loggedin-sequence.png-kbrb.png)

**Cross-Domain Login (Main Domain Not Logged In)**

![SSO System Design - Cross-Domain Login (Main Domain Not Logged In)](https://oss.javaguide.cn/github/javaguide/system-design/security/sso/sso-crossdomain-login-unlogin-sequence.png-kbrb.png)

**Cross-Domain Logout**

![SSO System Design - Cross-Domain Logout](https://oss.javaguide.cn/github/javaguide/system-design/security/sso/sso-crossdomain-logout-sequence.png-kbrb.png)

## Remarks

- Regarding the plan: This design plan mainly provides implementation ideas. If it involves APP user login, just add signature verification for the APP when accessing SSO services. Of course, if there is a wireless gateway, verifying the signature is not an issue.
- Regarding the sequence diagrams: The sequence diagrams do not cover all scenarios; they only list core/major scenarios. Additionally, we omit messages that do not affect the understanding of the concepts.

<!-- @include: @article-footer.snippet.md -->
