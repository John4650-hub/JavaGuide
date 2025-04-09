I don't...
title: JWT Identification Proficiency Analysis
Category: System design
Tag:
- Clear.
I don't...

In the course of the school interview, JWT was used for most candidate accreditation. Asking questions about JWT's conceptual issues and the reasons for using JWT is almost all answerable, but when asked about some problems and solutions that exist in JWT, only a small number of candidates can answer.

JWT is not a silver bullet; there are many flaws, and many times it's not the best option. In this article, let's look at the strengths and weaknesses of JWT identification and the solutions to common problems and see why many people no longer recommend JWT.

For a basic concept presentation on JWT, please read this article: [JWT Basic Concepts](https://javaguide.cn/system-design/security/jwt-intro.html).

# JWT's Advantage

The use of JWT for authentication has four main advantages in comparison to the way in which session authentication is conducted.

# No state #

JWT itself contains all the information required for authentication, so our server does not need to store JWT information. This clearly increases the availability and flexibility of the system and significantly reduces the pressure on the service side.

But it's precisely because of JWT's state of non-existence that it also leads to its greatest weakness: **Uncontrollable!**

For example, if we want to abandon a JWT or change its authority during the JWT period, it won't be effective immediately, usually after the validity period. For example, when the user logs out, JWT still works. Unless, at the back end, we add additional processing logic, such as storing invalid JWTs, and at the back end verify whether JWTs are effective before processing them. Concrete solutions, which we will elaborate on later, are simply mentioned here.

The CSRF attack was effectively averted.

**CSRF (Cross-Site Request Forgery)** is generally translated as **Cross-site request for forgery** and falls within the sphere of cyberattacks. CSRF visibility is not as high as SQL script injection, XSS, etc. But it is indeed a security hazard that we must take into account when developing systems. Gmail, a product of the industry’s technology pole Google, also had a CSRF loophole in 2007, which caused significant losses to Gmail users.

**So what is a cross-site request for forgery?** In short, it is doing something bad in your capacity.

To give a simple example: John went to an online bank, he came to the post section of the online bank, and he saw a link under a post that said, “Scientific finance, with an annual profit rate of over 10,000,” which was opened by the curious point of the big, and found his account to be $1000 short. Is that what happened? Turns out the hacker had a request hidden in the link, which sent a transfer request to the bank directly using his identity, that is, through your Cookie.

```html
<a href="http://www.mybank.com/Transfer?bankId=11&money=10,000">Scientific finance with an annual profitability of over</a>
```

CSRF attacks are dependent on Cookie; the `sessionID` in the session authentication of Cookie is sent to the service side by the browser, and Cookie will be carried on the request. By virtue of this characteristic, even if hackers are unable to access your `sessionID`, they can achieve the effect of an attack by mispointing the link.

Besides, it's not necessary to click on the link to achieve the impact of the attack, and many times when you open a page, the CSRF attack happens.

```html
<img src="http://www.mybank.com/Transfer?bankId=11&money=10,000"/>
```

**Then why doesn't JWT have such a problem?**

When we use JWT, we usually choose to store it in localStorage after we have successfully accessed JWT. Each request at the front end will be followed by this JWT, and the whole process will not involve Cookie at all. Therefore, even if you click an illegal link to send a request to the service, this illegal request will not carry JWT, so it will be illegal.

In conclusion, just one sentence: **The use of JWT for authentication does not need to rely on Cookie, so CSRF attacks can be avoided.**

However, there is also a risk of an XSS attack. In order to avoid an XSS attack, you can choose to store JWT in Cookie, marked `httpOnly`. But that leads to the fact that you have to provide CSRF protection on your own, so we don't usually do that in actual projects.

The common way to avoid an XSS attack is to filter out suspicious strings in the request where there is a risk of an XSS attack.

In the Spring project, we usually do this by creating XSS filters.

```java
@Component
@Order(Ordered.HIGHEST_PRECEDENCE)
public class XSSFilter extends Filter {

    @