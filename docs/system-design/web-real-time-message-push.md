---
title: Explanation of Web Real-Time Messaging Push
category: System Design
icon: messages
head:
  -   - meta
      - name: keywords
        content: messaging push, short polling, long polling, SSE, Websocket, MQTT
  -   - meta
      - name: description
        content: Messaging push usually refers to the active message push to users on websites or mobile device apps by operators through some tools.
---

> Original link: \<https://juejin.cn/post/7122014462181113887, JavaGuide> has provided a comprehensive summary of this article.

I have a friend who has created a small website, and he wants to implement a web messaging push feature for internal messages, specifically the small red dot shown in the image below, which is a commonly used feature.

![Web Messaging Push for Internal Messages](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/1460000042192380.png)

However, he hasn’t decided which method to use yet, so I’ve organized a few options and briefly implemented them.

## What is Messaging Push?

There are various scenarios for messaging push. For example, if someone follows my public account, I will receive a push notification to attract my attention and encourage me to open the application.

Messaging push typically refers to the proactive messaging sent by website operators to users on their current web pages or mobile device apps using particular tools.

Messaging push can be divided into web messaging push and mobile messaging push.

Example of mobile messaging push:

![Mobile Messaging Push Example](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/IKleJ9auR1Ojdicyr0bH.png)

Example of web messaging push:

![Web Messaging Push Example](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/image-20220819100512941.png)

Before we dive into the specific implementation, let’s analyze the requirements. The functionality is quite simple; as long as a specific event is triggered (such as actively sharing a resource or the backend actively pushing a message), the notification red dot on the web page will update in real-time (`+1`), and that’s it.

Typically, the server will have several messaging push tables to record the different types of messages pushed to users for various events. The frontend actively queries (pulls) or passively receives (pushes) the number of unread messages.

![Messaging Push Table](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/1460000042192384.png)

Messaging push mainly consists of two forms: push and pull. Let's explore them one by one.

## Common Messaging Push Solutions

### Short Polling

**Polling** is probably the simplest way to implement a messaging push solution, and we can divide polling into short polling and long polling.

Short polling is easy to understand, as it involves the browser sending HTTP requests to the server at specified time intervals, and the server immediately returns unread message data to the client, which is then rendered in the browser.

A simple JS timer can handle it; just request the unread message count interface once per second and display the returned data.

```typescript
setInterval(() => {
  // Method request
  messageCount().then((res) => {
    if (res.code === 200) {
      this.messageCount = res.data;
    }
  });
}, 1000);
```

The effect is acceptable. Although short polling is easy to implement, the obvious drawback is that the push data does not frequently change, and regardless of whether there are new messages from the backend, the client will continuously request, which will inevitably exert significant pressure on the server and waste bandwidth and server resources.

### Long Polling

Long polling is an improved version of short polling, designed to minimize wasted server resources while ensuring relative message real-time. Long polling is widely used in middleware, exemplified by Nacos and Apollo configuration centers, as well as in messaging queues like Kafka and RocketMQ.

In the article [Is Nacos Configuration Center Interaction Model Push or Pull?](https://mp.weixin.qq.com/s/94ftESkDoZI9gAGflLiGwg), I elaborated on the implementation principles of long polling in Nacos. Interested readers can check it out.

Long polling operates similarly to polling but with a key difference: if there is no data change on the server, the request remains on hold until there is a data change or a timeout occurs. Once data changes, the client immediately initiates another long polling request.

In this case, I implemented long polling using the Apollo configuration center, applying a class called `DeferredResult`, which is a mechanism for asynchronous requests provided by Spring after Servlet 3.0, essentially representing delayed results.

![Long Polling Diagram](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/1460000042192386.png)

`DeferredResult` allows the container thread to quickly release occupied resources without blocking the request thread, thus accommodating more requests and enhancing system throughput. After processing, it will call `DeferredResult.setResult(200)` to submit the response result.

Next, we will implement message push using long polling.

Because one ID may have multiple long polling requests, I employed the `Multimap` structure provided by the Guava package to store long polling requests, where a key can correspond to multiple values. Once a change is detected on the key, all corresponding long polling requests will respond. The frontend then receives a non-timeout status code, indicating data changes, and proactively queries the unread message count interface to update the page data.

```java
@Controller
@RequestMapping("/polling")
public class PollingController {

    // Store long polling collection that listens for a specific Id
    // Synchronized structure
    public static Multimap<String, DeferredResult<String>> watchRequests = Multimaps.synchronizedMultimap(HashMultimap.create());

    /**
     * Set up listening
     */
    @GetMapping(path = "watch/{id}")
    @ResponseBody
    public DeferredResult<String> watch(@PathVariable String id) {
        // Set timeout for the delayed object
        DeferredResult<String> deferredResult = new DeferredResult<>(TIME_OUT);
        // Remove the key when the asynchronous request is completed to prevent memory overflow
        deferredResult.onCompletion(() -> {
            watchRequests.remove(id, deferredResult);
        });
        // Register long polling request
        watchRequests.put(id, deferredResult);
        return deferredResult;
    }

    /**
     * Data changes
     */
    @GetMapping(path = "publish/{id}")
    @ResponseBody
    public String publish(@PathVariable String id) {
        // Data changes: retrieve all long polling requests listening to the ID and respond to them one by one
        if (watchRequests.containsKey(id)) {
            Collection<DeferredResult<String>> deferredResults = watchRequests.get(id);
            for (DeferredResult<String> deferredResult : deferredResults) {
                deferredResult.setResult("I updated at " + new Date());
            }
        }
        return "success";
    }
}
```

When the request exceeds the set timeout, an `AsyncRequestTimeoutException` is thrown. Here, I directly use `@ControllerAdvice` to globally capture and return a unified response, allowing the frontend to re-initiate long polling requests upon receiving the designated status code.

```kotlin
@ControllerAdvice
public class AsyncRequestTimeoutHandler {

    @ResponseStatus(HttpStatus.NOT_MODIFIED)
    @ResponseBody
    @ExceptionHandler(AsyncRequestTimeoutException.class)
    public String asyncRequestTimeoutHandler(AsyncRequestTimeoutException e) {
        System.out.println("Asynchronous request timed out");
        return "304";
    }
}
```

Let’s test it out. First, the webpage initiates a long polling request to `/polling/watch/10086` to listen for message changes. The request gets suspended without data updates until a timeout occurs, prompting another long polling request. Subsequently, when data is manually changed at `/polling/publish/10086`, long polling receives a response; the frontend executes business logic and then initiates the next request, creating a continuous loop.

Long polling significantly improves performance compared to short polling but still produces a considerable number of requests, which remains a limitation.

### Iframe Streaming

Iframe streaming involves inserting a hidden `<iframe>` tag into the page. By requesting the message count API in the `src`, a long connection is created between the server and the client, enabling the server to continuously transmit data to the `iframe`.

The transmitted data is typically HTML or embedded JavaScript script, allowing real-time updates to the page.

![Iframe Streaming Diagram](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/1460000042192388.png)

This method is simple to implement; the frontend only needs an `<iframe>` tag.

```html
<iframe src="/iframe/message" style="display:none"></iframe>
```

The server simply assembles the HTML and JS script data and writes it into the response.

```java
@Controller
@RequestMapping("/iframe")
public class IframeController {
    @GetMapping(path = "message")
    public void message(HttpServletResponse response) throws IOException, InterruptedException {
        while (true) {
            response.setHeader("Pragma", "no-cache");
            response.setDateHeader("Expires", 0);
            response.setHeader("Cache-Control", "no-cache,no-store");
            response.setStatus(HttpServletResponse.SC_OK);
            response.getWriter().print(" <script type=\"text/javascript\">\n" +
                    "parent.document.getElementById('clock').innerHTML = \"" + count.get() + "\";" +
                    "parent.document.getElementById('count').innerHTML = \"" + count.get() + "\";" +
                    "</script>");
        }
    }
}
```

Iframe streaming incurs a significant server load, and browsers like IE and Chrome continue to remain in a loading state, with the icon spinning persistently, which can be quite irritating.

![Iframe Streaming Effect](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/1460000042192389.png)

Iframe streaming is extremely unfriendly and is strongly not recommended.

### SSE (Recommended)

Many might not know that, besides `WebSocket`, there is another method for the server to push messages to the client: Server-Sent Events (SSE). This is a one-way messaging push from the server to the client (browser).

The well-known ChatGPT employs SSE. For dialogue scenarios requiring long wait times for responses, ChatGPT utilizes a clever strategy: it “pushes” pre-computed data to users, continuously returning data during the computation process using SSE technology. This approach avoids users closing the page due to excessive wait times.

![ChatGPT Using SSE for Dialogue](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/chatgpt-sse.png)

SSE is based on the HTTP protocol. Generally, standard HTTP protocols cannot push messages actively from the server to the client, but SSE is an exception with a different approach.

![](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/1460000042192390.png)

SSE opens a one-way channel between the server and the client, with server responses being stream-type data in `text/event-stream`, transmitting changes from the server to the client as data is modified.

The overall implementation is somewhat analogous to online video playback, where the video stream is continuously pushed to the browser. You can also think of it as the client completing a prolonged download (perhaps due to network issues).

![SSE Diagram](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/1460000042192391.png)

SSE functions similarly to WebSocket, allowing communication between the server and the browser to achieve server-to-client message pushes, but there are some differences:

- SSE is based on HTTP protocol; they work without special server implementation or protocols; WebSocket requires separate server processing for the protocol.
- SSE is one-way communication, meaning communication can only occur from the server to the client; WebSocket allows full-duplex communication where both parties can send and receive messages simultaneously.
- SSE is easy to implement with low development costs, requiring no additional components; WebSocket has higher development complexity due to the need for secondary data parsing.
- SSE automatically supports reconnections; WebSocket requires manual implementation.
- SSE can only transmit text messages; binary data must be encoded for transmission; WebSocket natively supports binary data transmission.

**How to Choose Between SSE and WebSocket?**

> There is no absolute distinction between good and bad technologies; it’s about finding the right fit.

SSE seems to be relatively unknown, partially because the appearance of WebSocket offered richer protocols for two-way, full-duplex communication. In scenarios like gaming, instant messaging, and applications requiring real-time updates in both directions, the two-way channel is more appealing.

However, in some cases, there is no need to send data from the client, and you only require updates from the server’s operations. For example: internal messages, unread message counts, status updates, stock prices, monitoring counts, etc. In these scenarios, SSE is more advantageous in terms of implementation difficulty and cost. Furthermore, SSE includes various functionalities that WebSocket lacks in design, such as automatic reconnections, event IDs, and the ability to send arbitrary events.

The frontend only needs to make one HTTP request, passing a unique ID, to open the event stream and listen for events pushed from the server.

```javascript
<script>
    let source = null;
    let userId = 7777;
    if (window.EventSource) {
        // Establish connection
        source = new EventSource('http://localhost:7777/sse/sub/' + userId);
        setMessageInnerHTML("Connected user=" + userId);
        /**
         * Once the connection is established, the open event will be triggered
         * Alternative syntax: source.onopen = function (event) {}
         */
        source.addEventListener('open', function (e) {
            setMessageInnerHTML("Connection established...");
        }, false);
        /**
         * Client receives data sent from the server
         * Alternative syntax: source.onmessage = function (event) {}
         */
        source.addEventListener('message', function (e) {
            setMessageInnerHTML(e.data);
        });
    } else {
        setMessageInnerHTML("Your browser does not support SSE");
    }
</script>
```

The server-side implementation is simpler; create a `SseEmitter` object and manage it within the `sseEmitterMap`.

```java
private static Map<String, SseEmitter> sseEmitterMap = new ConcurrentHashMap<>();

/**
 * Create connection
 */
public static SseEmitter connect(String userId) {
    try {
        // Set timeout, 0 means it does not expire. Default is 30 seconds.
        SseEmitter sseEmitter = new SseEmitter(0L);
        // Register callbacks
        sseEmitter.onCompletion(completionCallBack(userId));
        sseEmitter.onError(errorCallBack(userId));
        sseEmitter.onTimeout(timeoutCallBack(userId));
        sseEmitterMap.put(userId, sseEmitter);
        count.getAndIncrement();
        return sseEmitter;
    } catch (Exception e) {
        log.info("Exception creating new SSE connection, current user: {}", userId);
    }
    return null;
}

/**
 * Send messages to a designated user
 */
public static void sendMessage(String userId, String message) {
    if (sseEmitterMap.containsKey(userId)) {
        try {
            sseEmitterMap.get(userId).send(message);
        } catch (IOException e) {
            log.error("Push exception for user[{}]: {}", userId, e.getMessage());
            removeUser(userId);
        }
    }
}
```

**Note:** SSE is not supported by Internet Explorer, but it has good compatibility with other major browsers.

![SSE Compatibility](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/1460000042192393.png)

### WebSocket

WebSocket is likely one of the more familiar methods for pushing messages, as we already compared it to SSE earlier.

This is a protocol that enables full-duplex communication over a TCP connection, establishing a communication channel between the client and server. The browser and server need only a single handshake, creating a persistent connection that allows for bidirectional data transmission.

![WebSocket Diagram](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/1460000042192394.png)

WebSocket’s operational process can be summarized in the following steps:

1. The client sends an HTTP request to the server with headers including `Upgrade: websocket` and `Sec-WebSocket-Key`, indicating a request to upgrade the protocol to WebSocket.
1. Upon receiving the request, the server attempts to upgrade the protocol. If WebSocket is supported, it responds with an HTTP 101 status code, with headers indicating that the protocol upgrade was successful.
1. A WebSocket connection is established between the client and server, allowing for bidirectional data transmission. Data is sent in frames instead of traditional HTTP requests and responses. Each message may be divided into multiple data frames (smallest unit), the sender splits the message into several frames, which are sent to the receiver, who reassembles the frames into the complete message.
1. Either the client or server can actively send a close frame, indicating the desire to disconnect. The other party will respond with a close frame, and both sides will terminate the TCP connection.

Additionally, once a WebSocket connection is established, a heartbeat mechanism is employed to maintain the WebSocket connection's stability and activity.

To integrate WebSocket with SpringBoot, include the related WebSocket packages. Compared to SSE, there is additional development overhead.

```xml
<!-- Include websocket -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```

The server utilizes the `@ServerEndpoint` annotation to designate the current class as a WebSocket server, with clients able to connect through `ws://localhost:7777/webSocket/10086`.

```java
@Component
@Slf4j
@ServerEndpoint("/websocket/{userId}")
public class WebSocketServer {
    // Connection session with a particular client, used to send data to the client
    private Session session;
    private static final CopyOnWriteArraySet<WebSocketServer> webSockets = new CopyOnWriteArraySet<>();
    // To keep track of online connection counts
    private static final Map<String, Session> sessionPool = new HashMap<>();

    /**
     * Method called upon successful connection
     */
    @OnOpen
    public void onOpen(Session session, @PathParam(value = "userId") String userId) {
        try {
            this.session = session;
            webSockets.add(this);
            sessionPool.put(userId, session);
            log.info("WebSocket message: New connection established, total count: " + webSockets.size());
        } catch (Exception e) {
        }
    }

    /**
     * Method called when a message is received from the client
     */
    @OnMessage
    public void onMessage(String message) {
        log.info("WebSocket message: Received message from client: " + message);
    }

    /**
     * A method for sending a single message
     */
    public void sendOneMessage(String userId, String message) {
        Session session = sessionPool.get(userId);
        if (session != null && session.isOpen()) {
            try {
                log.info("WebSocket message: Sending single point message: " + message);
                session.getAsyncRemote().sendText(message);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

The server must also inject `ServerEndpointExporter`; this bean automatically registers WebSocket servers annotated with `@ServerEndpoint`.

```java
@Configuration
public class WebSocketConfiguration {

    /**
     * Registers WebSocket servers annotated with @ServerEndpoint
     */
    @Bean
    public ServerEndpointExporter serverEndpointExporter() {
        return new ServerEndpointExporter();
    }
}
```

The frontend initializes and opens a WebSocket connection, listens for connection status, receives data from the server, or sends data to the server.

```javascript
<script>
    var ws = new WebSocket('ws://localhost:7777/webSocket/10086');
    // Retrieve the connection status
    console.log('WS connection status: ' + ws.readyState);
    // Listen for successful connection
    ws.onopen = function () {
        console.log('WS connection status: ' + ws.readyState);
        // Upon successful connection, send a message
        ws.send('test1');
    }
    // Listen for messages from the server and process them
    ws.onmessage = function (data) {
        console.log('Received message from the server:');
        console.log(data);
        // After the communication is complete, close the WebSocket connection
        ws.close();
    }
    // Listen for connection close events
    ws.onclose = function () {
        // Monitor the WebSocket status throughout the process
        console.log('WS connection status: ' + ws.readyState);
    }
    // Listen for and handle error events
    ws.onerror = function (error) {
        console.log(error);
    }
    function sendMessage() {
        var content = $("#message").val();
        $.ajax({
            url: '/socket/publish?userId=10086&message=' + content,
            type: 'GET',
            data: { "id": "7777", "content": content },
            success: function (data) {
                console.log(data)
            }
        })
    }
</script>
```

Upon initializing the page to establish a WebSocket connection, bidirectional communication can then take place, with satisfactory results.

![](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/1460000042192395.png)

### MQTT

**What is the MQTT Protocol?**

MQTT (Message Queue Telemetry Transport) is a lightweight messaging protocol based on the publish/subscribe model. By subscribing to specific topics, messages can be received. It is a standard transport protocol in the Internet of Things (IoT).

This protocol separates message publishers from subscribers, thereby providing reliable message services for remotely connected devices in unreliable network environments, similar to traditional MQ.

![MQTT Protocol Example](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/1460000022986325.png)

The TCP protocol is located at the transport layer, while the MQTT protocol operates at the application layer. MQTT is built on top of the TCP/IP protocol. This means that as long as the TCP/IP protocol stack is supported, the MQTT protocol can be utilized.

**Why Use the MQTT Protocol?**

Why is MQTT particularly favored in IoT applications compared to other protocols, such as our more familiar HTTP protocol?

- First, the HTTP protocol is synchronous; the client must wait for the server's response after making a request. In IoT environments, devices are often impacted by environmental factors such as low bandwidth, high network latency, and unstable communications. Clearly, asynchronous messaging protocols are better suited for IoT applications.
- HTTP is unidirectional; clients must initiate a connection to obtain messages. However, in IoT applications, devices and sensors often act as clients, meaning they cannot passively receive commands from the network.
- Often, commands or messages need to be sent to all devices on the network. This is not only difficult to achieve with HTTP but also incurs quite a high cost.

For a detailed introduction and practical use of the MQTT protocol, I will not elaborate further here; you can refer to my previous articles, which are also quite detailed.

- Introduction to the MQTT Protocol: [I Never Expected That SpringBoot + RabbitMQ Would Make Smart Home Development So Simple](https://mp.weixin.qq.com/s/udFE6k9pPetIWsa6KeErrA)
- Implementing Messaging Push via MQTT: [Unread Messages (Small Red Dot): Frontend Real-Time Messaging Push Practice with RabbitMQ, Incredibly Simple~](https://mp.weixin.qq.com/s/U-fUGr9i1MVa4PoVyiDFCg)

## Summary

> The following content is an addition from JavaGuide

|                  | Description                                                                                                                      | Advantages                                           | Disadvantages                                                                                                |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| Short Polling    | Client regularly sends requests to the server, and the server directly returns response data (even without updates)              | Simple, easy to understand and implement             | Poor real-time capability, too many ineffective requests, and frequent connections consuming resources       |
| Long Polling     | Unlike short polling, long polling holds off requests until data updates are available to return them                            | Reduces ineffective requests                         | Suspended requests can lead to resource wastage                                                              |
| Iframe Streaming | Establishes a long connection between the server and the client, continuously transmitting data to `<iframe>`.                   | Simple, easy to understand and implement             | Maintaining a long connection increases cost, with poor visual results (the loading icon spins continuously) |
| SSE              | A one-way messaging push from server to client (browser).                                                                        | Simple, easy to implement, and rich in functionality | Does not support bidirectional communication                                                                 |
| WebSocket        | Uses HTTP protocol only during initial connection setup, otherwise communicates directly over TCP for full-duplex communication. | High performance, low overhead                       | Higher requirements for developers, relatively complex implementation                                        |
| MQTT             | A lightweight messaging protocol based on publish/subscribe model, subscribing to relevant topics to receive messages.           | Mature, stable, and lightweight                      | Higher requirements for developers, relatively complex implementation                                        |

<!-- @include: @article-footer.snippet.md -->
