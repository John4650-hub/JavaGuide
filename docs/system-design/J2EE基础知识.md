# Servlet Summary

In Java Web applications, **Servlets** are mainly responsible for receiving user requests via `HttpServletRequest`, processing them in `doGet()` and `doPost()`, and providing responses through `HttpServletResponse` back to the user. **Servlets** can set initialization parameters for internal use. A Servlet class will have only one instance, initialized with the `init()` method and destroyed with the `destroy()` method\*\*.\*\* Servlets need to be configured in `web.xml` (creating a Servlet in MyEclipse automatically configures this), and **a Servlet can be assigned multiple URL access points**. **Servlets are not thread-safe**, so class variables should be used cautiously.

## What are the differences between Servlet and CGI?

### Drawbacks of CGI

1. It requires starting a system process for each request to run the CGI program, which can lead to significant overhead if requests are frequent.

1. It requires loading and running a CGI program for each request, adding to the overhead.

1. It necessitates repeatedly writing code to handle network protocols and encoding, which is very time-consuming.

### Advantages of Servlet

1. Only one operating system process and one JVM need to be loaded, significantly reducing system overhead.

1. When multiple requests require the same processing, only one class needs to be loaded, further lowering overhead.

1. All dynamically loaded classes can share network protocol handling and request decoding, greatly reducing the workload.

1. Servlets can directly interact with the Web server, unlike standard CGI programs. Servlets can also share data between different programs, making functionalities like a database connection pool easy to implement.

Supplement: Sun Microsystems released Servlet technology in 1996 to compete with CGI. Servlets are a special kind of Java program; a Java-based Web application typically includes one or more Servlet classes. Servlets cannot be created and executed independently; they run in a Servlet container, which passes user requests to the Servlet program and returns the Servlet's response to the user. Generally, a Servlet is associated with one or more JSP pages. CGI has frequently been criticized for performance overhead; however, Fast CGI has already addressed CGI's efficiency issues, so it's not necessary to disparage CGI mindlessly in interviews. In fact, many websites you are familiar with use CGI technology.

Reference: "Java Web Integration Development: The Return of the King" P7

## What methods are defined in the Servlet interface and the exploration of the Servlet lifecycle

The Servlet interface defines 5 methods, of which **the first three are related to the Servlet lifecycle**:

- `void init(ServletConfig config) throws ServletException`
- `void service(ServletRequest req, ServletResponse resp) throws ServletException, java.io.IOException`
- `void destroy()`
- `java.lang.String getServletInfo()`
- `ServletConfig getServletConfig()`

**Lifecycle:** **The Servlet lifecycle begins after the Web container loads and instantiates the Servlet.** The container runs its **init() method** for initialization; when a request arrives, it calls the Servlet's **service() method**, which may invoke the corresponding **doGet or doPost** methods as needed; when the server shuts down or a project is undeployed, the server destroys the Servlet instance, calling the Servlet's **destroy() method**. **The init and destroy methods are executed only once, while the service method is executed for every client request to the Servlet**. In some cases, Servlets may need to use resources that require initialization and cleanup, so the resource initialization code can be placed in the init method, and the resource cleanup code in the destroy method, eliminating the need to initialize and clean up resources with each client request.

Reference: "Java Web Integration Development: The Return of the King" P81

## The difference between GET and POST

This question has been discussed widely on Zhihu: <https://www.zhihu.com/question/28586791>.

![](https://static001.geekbang.org/infoq/04/0454a5fff1437c32754f1dfcc3881148.png)

GET and POST are two commonly used request methods in the HTTP protocol, each with different characteristics and usages depending on the scenario and purpose. Generally, they can be distinguished from the following aspects:

- Semantic difference: GET is typically used to retrieve or query resources, while POST is usually used to create or modify resources. GET requests should be idempotent, meaning that executing them multiple times won’t change the resource's state, whereas POST requests may have side effects, meaning that each execution could yield different results or alter the resource's state.
- Format difference: GET request parameters are usually included in the URL as a query string, while POST request parameters reside in the request body and can use various encoding formats such as application/x-www-form-urlencoded, multipart/form-data, application/json, etc. The length of GET request URLs is limited by browsers and servers, but POST bodies do not have a strict size limit.
- Caching difference: Since GET requests are idempotent, they can be cached by browsers or intermediary nodes (such as proxies and gateways) to improve performance and efficiency. POST requests, on the other hand, are generally not suitable for caching because of potential side effects that may require real-time responses on each execution.
- Security difference: Neither GET nor POST requests are absolutely secure, as HTTP protocol transmission occurs in plaintext, meaning URLs, headers, and bodies can be intercepted or tampered with. To ensure security, HTTPS protocols must be used to encrypt data during transmission. However, in some cases, GET requests might be more likely to leak sensitive data compared to POST requests because GET request parameters appear in the URL, which may be recorded in browser histories, server logs, proxy logs, etc. Therefore, for private data transmission, it is generally recommended to use POST with body parameters.

Focus on clarifying the semantic differences between the two. However, some projects may choose to use POST for all requests, which is not fixed and should be agreed upon by the project team.

## When to call doGet() and doPost()

The method in the form tag is set to get when calling doGet(), and post when calling doPost().

## Differences between Forward and Redirect

**Forward is a server-side action, while redirect is a client-side action.**

**Forward**
This is implemented through the forward() method of the RequestDispatcher object (`HttpServletRequest request, HttpServletResponse response`). The RequestDispatcher can be obtained via the `getRequestDispatcher()` method of HttpServletRequest. For example, the following code redirects to the login_success.jsp page.

```java
request.getRequestDispatcher("login_success.jsp").forward(request, response);
```

**Redirect** is achieved by returning a status code from the server. When the client browser requests the server, the server returns a status code. The server sets the status code using `HttpServletResponse`'s `setStatus(int status)` method. If the server returns 301 or 302, the browser will request the resource at the new URL.

1. **From the address bar display perspective**

   In a forward request, the server requests the resource and directly accesses the target URL, reading its response and sending it back to the browser. The browser is unaware of where the content sent by the server comes from, so its address bar remains unchanged.
   In a redirect scenario, the server sends a status code based on logic, instructing the browser to request that address anew. Thus, the address bar reflects the new URL.

1. **From data sharing perspective**

   In a forward request, the forwarding page and the destination page can share data within the request.
   In a redirect scenario, data cannot be shared.

1. **From usage perspective**

   Forwarding requests are typically used for user logins, redirecting to appropriate modules based on user roles.
   Redirects are generally used for logging users out and returning to the main page or redirecting to other websites.

1. **From efficiency perspective**

   Forward: High.
   Redirect: Low.

## Auto Refresh

Auto-refresh can not only redirect to another page after a certain period but can also refresh the current page automatically after some time. In Servlets, this can be set using the Header property of the HttpServletResponse object, for example:

```java
Response.setHeader("Refresh","5;URL=http://localhost:8080/servlet/example.htm");
```

Here, 5 represents the time in seconds. The URL specified is the page to redirect to (if set to its own path, the page will refresh every 5 seconds).

## Servlet and Thread Safety

**Servlets are not thread-safe; concurrent read/write can lead to data inconsistency issues.** The solution is to avoid defining name attributes and instead declare the name variable within the doGet() and doPost() methods. Although using synchronized(name){} blocks can resolve issues, it causes thread waits, which is not an efficient solution.
Note: Concurrent read/write of Servlet class attributes can lead to data inconsistency. However, if only reading attributes concurrently without writing, there are no data consistency issues. Therefore, read-only attributes in Servlets are best defined as final.

Reference: "Java Web Integration Development: The Return of the King" P92

## What is the relationship between JSP and Servlets?

This question has actually been explained above. Servlets are special Java programs that run in the server's JVM and depend on server support to deliver displaying content to browsers. JSP is essentially a simplified form of Servlet; it is processed by the server into a Java program similar to a Servlet, which simplifies the generation of page content. The main difference between Servlets and JSP is that the application logic in Servlets is contained in Java files and is completely separated from the HTML in the presentation layer. In contrast, JSP allows Java and HTML to be combined into a file with the .jsp extension. Some say that Servlets write HTML in Java while JSP allows Java code to be written in HTML; however, this characterization is very one-sided and not accurate. JSP focuses on the view while Servlets concentrate on control logic. In the MVC architectural pattern, JSP is suited to serve as the view, while Servlets are suited to act as controllers.

## JSP Working Principle

JSP is a type of Servlet, but its operation differs from HttpServlet. HttpServlet is first compiled into a class file and then deployed on the server, meaning it is compiled before deployment. In contrast, JSP is deployed first and then compiled. When a client requests the JSP file for the first time, it is compiled into the HttpJspPage class (a subclass of the Servlet interface). This class will be temporarily stored in the server’s working directory. Below is an example to illustrate this.
In the JspLoginDemo project, there is a Jsp file named login.jsp. After deploying this project on the server for the first time and accessing this JSP file, we notice that there are now two additional items in this directory as shown below.
The .class file is the corresponding Servlet for the JSP. After compiling, the class file runs to respond to client requests. When a client accesses login.jsp in the future, Tomcat will no longer recompile the JSP file but will directly invoke the class file to respond to the client request.

![JSP Working Principle](https://oss.javaguide.cn/github/javaguide/1.jpeg)

Since JSP is only compiled upon the first request from the client, the initial request might feel slower, but subsequent requests will be significantly faster. If the server saves the class file and it is deleted later, the server will recompile the JSP again.

When developing Web applications, JSP files are frequently modified. Tomcat automatically detects changes to JSP programs, and if it detects changes in JSP source code, it will recompile the JSP on the next client request, without requiring Tomcat to be restarted. This auto-detection feature is enabled by default and takes a small amount of time during deployment. It can be disabled in web.xml when deploying Web applications.

Reference: "Java Web Integration Development: The Return of the King" P97

## What are the built-in objects of JSP, and what are their roles?

[JSP Built-in Objects - CSDN Blog](http://blog.csdn.net/qq_34337272/article/details/64310849)

JSP has 9 built-in objects:

- request: Encapsulates the client's request, including parameters from GET or POST requests;
- response: Encapsulates the server's response to the client;
- pageContext: This object allows access to other objects;
- session: Encapsulates the user session object;
- application: Encapsulates the server runtime environment object;
- out: Outputs the server response's output stream;
- config: Web application's configuration object;
- page: The JSP page itself (analogous to this in Java);
- exception: Encapsulates exceptions thrown from the page.

## What are the main methods of the Request object?

- `setAttribute(String name,Object)`: Sets the request parameter value with the name specified by name.
- `getAttribute(String name)`: Returns the attribute value specified by name.
- `getAttributeNames()`: Returns a collection of names of all attributes in the request object, resulting in an instance of an enumeration.
- `getCookies()`: Returns all Cookie objects from the client, resulting in an array of Cookies.
- `getCharacterEncoding()`: Returns the character encoding of the request.
- `getContentLength()`: Returns the length of the request body.
- `getHeader(String name)`: Obtains HTTP protocol-defined header information.
- `getHeaders(String name)`: Returns all values of the request header specified by name, resulting in an instance of an enumeration.
- `getHeaderNames()`: Returns the names of all request headers, resulting in an instance of an enumeration.
- `getInputStream()`: Returns the input stream of the request used to access the data in the request.
- `getMethod()`: Obtains the method used by the client to send data to the server.
- `getParameter(String name)`: Obtains the parameter value sent by the client to the server specified by name.
- `getParameterNames()`: Obtains all parameter names sent by the client to the server, resulting in an instance of an enumeration.
- `getParameterValues(String name)`: Obtains all values for the parameter specified by name.
- `getProtocol()`: Retrieves the protocol name upon which the client sends data to the server.
- `getQueryString()`: Obtains the query string.
- `getRequestURI()`: Retrieves the client address that made the request.
- `getRemoteAddr()`: Retrieves the client's IP address.
- `getRemoteHost()`: Retrieves the client's host name.
- `getSession([Boolean create])`: Returns the session related to the request.
- `getServerName()`: Retrieves the server name.
- `getServletPath()`: Retrieves the path of the script file requested by the client.
- `getServerPort()`: Retrieves the server's port number.
- `removeAttribute(String name)`: Deletes an attribute from the request.

## What is the difference between request.getAttribute() and request.getParameter()?

**From the perspective of acquisition direction:**

`getParameter()` retrieves parameter values passed via POST/GET;

`getAttribute()` retrieves data values from the object container.

**From the perspective of usage:**

`getParameter()` is utilized during client-side redirection, meaning it is used to receive values when a link is clicked or a button is submitted, such as using form data or URL redirection.

`getAttribute()` is for server-side redirection, commonly used in servlets utilizing the forward function or in Struts with mapping.findForward. `getAttribute()` can only receive values passed through setAttribute in the program.

Moreover, you can use `setAttribute()`, `getAttribute()` to send and receive objects, whereas `getParameter()` is clearly limited to passing strings. `setAttribute()` allows the application server to place this object into a specific memory segment linked to the page. When your page server redirects to another page, the application server will copy this memory segment to the memory corresponding to the new page. Thus, `getAttribute()` can obtain the set value. This method can pass objects. The same applies to sessions, but the lifecycle of the object in memory differs. `getParameter()` is simply the application server analyzing the textual content from the request page to retrieve the values set during form or URL redirection.

**Summary:**

`getParameter()` returns a String, used to read values from submitted forms (which can then be converted to necessary data types as needed, like integer, date type, etc.)

`getAttribute()` returns an Object, which needs conversion; you can set it up with `setAttribute()` for any object, offering great flexibility for use.

## The behavior differences of include directive

**Include directive:** JSP can include other files using the include directive. The included files can be JSP files, HTML files, or text files. The included file behaves as if it is part of the JSP file and will be compiled and executed simultaneously. The syntax is as follows:
\<%@ include file="relative URL address of the file" %>

**Include action:** The `<jsp:include>` action element includes both static and dynamic files. This action inserts the specified file into the page being generated. The syntax is as follows:
\<jsp:include page="relative URL address" flush="true" />

## Summary of JSP's nine built-in objects, seven actions, and three directives

[JSP's nine built-in objects, seven actions, and three directives summary](http://blog.csdn.net/qq_34337272/article/details/64310849)

## Explanation of the four scopes in JSP

The four scopes in JSP are page, request, session, and application, specifically:

- **page** represents objects and attributes related to a single page.
- **request** represents objects and attributes relating to a request made by a Web client. A request may span multiple pages and involve multiple Web components, so temporary data for display on a page can be placed in this scope.
- **session** represents objects and attributes related to a session established between a user and the server. Data pertaining to a specific user should be stored in that user’s session.
- **application** represents objects and attributes related to the entire Web application; it essentially serves as a global scope that spans across multiple pages, requests, and sessions.

## How to implement single-thread mode in JSP or Servlet

For JSP pages, this can be set via the page directive:
`<%@page isThreadSafe="false"%>`

For Servlets, custom Servlets can implement the SingleThreadModel marker interface.

Note: Setting JSP or Servlets to single-thread working mode leads to the creation of one Servlet instance per request, which can severely impact performance (increased server memory pressure and frequent garbage collection). Therefore, this approach is typically not adopted.

## What are the techniques for session tracking?

1. **Using Cookies**

   Sending cookies to the client

   ```java
   Cookie c = new Cookie("name", "value"); // Create Cookie
   c.setMaxAge(60*60*24); // Set maximum age, e.g., one day
   response.addCookie(c); // Add the Cookie to the HTTP response
   ```

   Reading cookies from the client

   ```java
   String name = "name";
   Cookie[] cookies = request.getCookies();
   if (cookies != null) {
       for (int i = 0; i < cookies.length; i++) {
           Cookie cookie = cookies[i];
           if (name.equals(cookie.getName())) {
               // Do something here.
               // You can get the value
               cookie.getValue();
           }
       }
   }
   ```

   **Advantages:** Data can be persistently saved, does not require server resources, is simple, and is based on key-value textual data.

   **Disadvantages:** Size limitations, users can disable Cookie functionality, and due to local storage, there are certain security risks.

1. URL Rewriting

   Adding user session information as a request parameter in the URL, or appending a unique session ID to the end of the URL to identify a session.

   **Advantages:** Usable even when cookies are disabled.

   **Disadvantages:** Requires URL encoding of the website; all pages must be generated dynamically and cannot be accessed via pre-recorded URLs.

1. Hidden Form Fields

   ```html
   <input type="hidden" name="session" value="..." />
   ```

   **Advantages:** Usable when cookies are disabled.

   **Disadvantages:** All pages must result from a form submission.

1. HttpSession

   Among all session tracking technologies, the HttpSession object is the most robust and feature-rich. An HttpSession is automatically created when a user first visits a website, allowing each user to access their unique HttpSession. You can obtain HttpSession through the HttpServletRequest object's getSession method, set a value within HttpSession using the setAttribute method, and retrieve an object saved in HttpSession by calling the getAttribute method with the property name provided. Unlike the previous three methods, HttpSession resides in the server's memory, so large objects should not be placed inside it. Even if current Servlet containers can move objects from HttpSession to other storage devices when memory is full, this will certainly affect performance. The value added to HttpSession can be any Java object, ideally implementing the Serializable interface, so that when necessary, the Servlet container can serialize it to a file, otherwise, an exception may occur during serialization.

## Differences between Cookie and Session

Both Cookie and Session are methods for tracking browser user identities, but their application scenarios differ.

**Cookies are generally used to save user information**, such as: ① Saving logged-in user information in cookies allows automatic filling of basic information during subsequent visits; ② Typically, websites have a "stay logged in" option, meaning that when you revisit the site, you won't need to log in again, as a token was stored in a cookie when you logged in, and it can be looked up based on the token (for security reasons, generating a new token upon the next login is common); ③ Logging in once allows for subsequent access to different pages of the website without needing to log in again. **Sessions primarily serve to record user states on the server side.** A typical scenario is the shopping cart; the system cannot determine which user is adding items to the cart, as HTTP is stateless. After creating a specific Session for a user, the user can be identified and tracked.

Cookie data is stored on the client (browser), while Session data is stored on the server.

Since cookies are stored on the client, while Session data is stored on the server, session security is generally higher. Sensitive information should not be written in cookies; it is best to encrypt cookie information and decrypt it on the server when needed.

<!-- @include: @article-footer.snippet.md -->
