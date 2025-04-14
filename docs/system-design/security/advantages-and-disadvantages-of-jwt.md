---
title: Analysis of the Advantages and Disadvantages of JWT Authentication
category: System Design
tag:
  - Security
---

In campus recruitment interviews, most candidates use JWT for authentication and login. When asked conceptual questions about JWT and the reasons for using it, they can generally provide some answers. However, when it comes to discussing the issues with JWT and their solutions, only a small portion of candidates respond adequately.

JWT is not a silver bullet; it has many flaws and is often not the optimal choice. In this article, we will explore the advantages and disadvantages of JWT authentication, as well as common problems and their solutions, to understand why many people no longer recommend using JWT.

For a basic introduction to JWT, please refer to my article: [Detailed Explanation of JWT Basics](https://javaguide.cn/system-design/security/jwt-intro.html).

## Advantages of JWT

Compared to session-based authentication, using JWT for identity verification has the following four main advantages.

### Stateless

JWT contains all the information needed for authentication, so our server does not need to store JWT information. This clearly increases the system's availability and scalability, significantly reducing the pressure on the server.

However, the stateless nature of JWT also leads to its biggest drawback: **lack of control!**

For example, if we want to invalidate a JWT or change its permissions during its validity period, it will not take effect immediately; typically, we have to wait until the expiration period is over. Additionally, when a user logs out, the JWT is still valid. Unless we add extra processing logic on the backend, such as storing invalidated JWTs and verifying their validity before processing, this issue remains. We will discuss specific solutions in detail later; for now, just a brief mention.

### Effectively Prevents CSRF Attacks

**CSRF (Cross-Site Request Forgery)** is generally translated as **Cross-Site Request Forgery**, which falls under the category of network attacks. Compared to security attacks like SQL injection and XSS, CSRF is not as well-known. However, it is indeed a security risk that we must consider when developing systems. Even Google's product Gmail experienced a CSRF vulnerability in 2007, causing significant losses for its users.

**So, what exactly is Cross-Site Request Forgery?** In simple terms, it is using your identity to perform malicious actions (sending unfriendly requests, such as unauthorized fund transfers).

For example: Xiao Zhuang logs into an online bank. He visits the bank's forum and sees a post with a link saying "Scientific Financial Management, Annual Profit Rate Over Ten Thousand." Curious, he clicks the link and finds that his account has decreased by 10,000 yuan. What happened? The hacker embedded a request in the link that directly used Xiao Zhuang's identity to send a transfer request to the bank, essentially making a request to the bank using his Cookie.

```html
<a src="http://www.mybank.com/Transfer?bankId=11&money=10000"
  >Scientific Financial Management, Annual Profit Rate Over Ten Thousand</a
>
```

CSRF attacks rely on Cookies. In session-based authentication, the `SessionID` in the Cookie is sent to the server by the browser; as long as a request is made, the Cookie will be included. Taking advantage of this feature, even if hackers cannot obtain your `SessionID`, they can still achieve an attack by tricking you into clicking an attack link.

Moreover, it is not necessary to click a link for an attack to occur; often, simply opening a certain page can trigger a CSRF attack.

```html
<img src="http://www.mybank.com/Transfer?bankId=11&money=10000" />
```

**So why doesn't JWT have this problem?**

Generally, when we use JWT, after successfully logging in and obtaining the JWT, we typically store it in localStorage. Each subsequent request from the frontend will include this JWT, and the entire process does not involve Cookies at all. Therefore, even if you click on a malicious link that sends a request to the server, this illegal request will not carry the JWT, making it invalid.

In summary: **Using JWT for authentication does not rely on Cookies, thus avoiding CSRF attacks.**

However, this also introduces the risk of XSS attacks. To mitigate XSS attacks, you can choose to store the JWT in a Cookie marked as `httpOnly`. However, this means you must provide your own CSRF protection, which is why we typically do not do this in actual projects.

Common methods to prevent XSS attacks include filtering out suspicious strings that pose a risk of XSS attacks in requests.

In Spring projects, we generally implement this by creating an XSS filter.

```java
@Component
@Order(Ordered.HIGHEST_PRECEDENCE)
public class XSSFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response,
      FilterChain chain) throws IOException, ServletException {
```
