# Servlet Summary

In the Java Web process, **Servlet** is primarily responsible for receiving user requests `HttpServletRequest`, in `doGet()`, `doPost()`, and providing feedback to users in response to `HttpServletResponse`. **Servlet** can set initialization parameters for internal use. A Servlet category can only provide an example of an `init()` method at its inception and a `destroy()` method at its destruction. **Servlet needs to be configured in web.xml (Servlet is automatically configured in MyEclipse)**, and a Servlet can set up multiple URLs. **Servlet is not thread-safe** and therefore the class variable should be used with caution.

# Describe the difference between Servlet and CGI?

# CGI's shortcomings

1. A system process to operate the CGI program needs to be initiated for each request. Should requests be frequent, this would entail significant costs.

1. It needs to load and run a CGI application for each request, which will entail significant costs.

1. There is a need to duplicate the code for processing network protocols, which is time-consuming.

# The virtue of Servlet

1. It just starts an operating system process and loads a JVM, which significantly reduces the cost of the system.

1. If multiple requests need to be handled the same way, then only one category will have to be loaded at this time, which greatly reduces the cost.

1. All dynamic load categories can achieve the sharing of network protocols and requests for decoding, significantly reducing the workload.

1. Servlet can interact directly with the Web server, while the normal CGI program cannot. Servlet also allows for the sharing of data between various programs, making it easy to perform functions such as database connection pools.

Supplement: In 1996, Sun Microsystems issued Servlet technology to compete with CGI, which is a special Java program, and a Java-based Web application usually contains one or more Servlet categories. Servlet is unable to create and execute itself. It runs in the Servlet container, which transmits the user's request to the Servlet program and returns the Servlet response to the user. Usually, one Server will associate one or more JSP pages. In the past, CGI was often stigmatized for performance problems, but Fast CGI had already solved the CGI problem of efficiency, so it was not necessary to talk about CGI in interviews, and in fact, many of your familiar websites used CGI technology.

Reference: Javaweb King of Integration and Development Return P7

# Servlet interfaces with methods and Servlet life-cycle explorers

The Servlet interface defines five methods, **of which the first three are relevant to the Servlet life cycle**:

- `void init(ServletConfig config) throws ServletException`
- `void service(ServletRequest req, ServletResponse resp) throws ServletException, java.io.IOException`
- `void destroy()`
- `java.lang.String getServletInfo()`
- `ServletConfig getServletConfig()`

**Life cycle:** **Web container loads and develops the example of Servlet,** the life cycle of Servlet begins, **the packaging operates its `init()` method** for the initialization of Servlet; **`service()` for the request to call Servlet**, `service()` for the request to call the corresponding **`doGet` or `doPost` method, as required; the server destroys the Servlet example when the server closes or the project is unloaded, and calls upon Servlet's `destroy()` method.** **`init` method and `destroy` method will only be executed once, and `service` method will be called every time a client requests Servlet.** Some resources requiring initialization and destruction are sometimes used in Servlet, so that the initialized resource code can be placed in the `init` method and the destruction resource code can be placed in the `destroy` method, so that there is no need to initialize and destroy the resource at each request of the client.

Reference: Javaweb Return of the Master of Integrated Development P81

# The difference between GET and POST

The issue was discussed with great enthusiasm at <https://www.zhihu.com/question/28586791>.

(https://static001.geekbang.org/infoq/04/0454a5fff1437c32754f1dfcc3881148.png)

GET and POST are the two most common methods of request in the HTTP protocol, which have different characteristics and usages under different scenarios and purposes. In general, they can be distinguished from:

- **Semantic differences:** GET is usually used to acquire or query resources, whereas POST is usually used to create or modify resources. GET requests should be idempotent, i.e., repeated execution will not change the status of resources, whereas POST requests may have side effects, i.e., the status of each execution may have different results or affect resources.
- **Differences in format:** The parameters requested by GET are usually placed in URLs and form a query string (query string), while the parameters requested by POST are usually placed in the request body and can be used in multiple coded formats, such as application/x-www-form-urlencoded, multipart/form-data, application
