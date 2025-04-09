I don't...
Title: Web Real-Time Messages Send Details
Category: System Design
icon: "messages"
Head:

- Meta.
- Name: keywords
  Contact: Message sent, Short Round, Long Round, SSE, WebSocket, MQTT
- Meta.
- Name: description
  Contact: Message delivery usually refers to people, such as the operation of the website, who use a tool to send active messages to the user's current web page or mobile APP.
  I don't...

This paper is well summarized at \<https://juejin.cn/post/712201446218113887, JavaGuide>.

I had a friend who made a little spot, and now we're going to do a Web message feed function, right, a little red dot, a very common feature.

(https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/14600004219238.png)

But he hasn't figured out how to do it yet, and I've worked out a few options for him here, and I've done it very simply.

# What's the Message Sent?

There's a lot of scenes, like people watching my public sign, and then I get a text message that's going to get me to click on the app.

Message delivery usually refers to people, such as the operation of the website, who are using some tool to send active messages to the user's current web page or mobile APP.

Messages are generally divided into web-based messages and mobile messages.

Example:

![Moved-end message push examples](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/IKleJ9auR1Ojdicyr0bH.png)

Web end messages send examples:

![Webendmail tweets for example](https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/image-2022081910051941.png)

Before we actually do that, let's analyze the needs in the front, which are simple, and just trigger an event (involved to share resources or backstage to send messages), the little red dot on the web page will be real-time.

Usually, there are several dispatch forms at the service end to record the different types of messages sent by the user that trigger different events, the front-end active query (rare) or passive receipt (push) of all unread messages from the user.

(https://oss.javaguide.cn/github/javaguide/system-design/web-real-time-message-push/1460000422384.png)

The news is in the form of push and pull, and we know it from one to the next.

# Message Sent to Common Scheme

# Shorter Round of Questions

**Polling** should be the simplest way to get the message out, and here we divide it into short and long.

The short-cycle query is well understood, with the specified time interval, the browser sends an HTTP request to the server, the server returns unread message data to the client in real-time, and the browser re-renders.

A simple JS timer can be done, requesting an unread message number interface every second, returning the data display.

```typescript
setInterval(() => {
  // Methodological request
  MessageCount().then((res) => {
    if (res.code == 200) {
      this.messageCount = res.data;
    }
  });
}, 1000);
```

It is possible that short-duration queries will be achieved, although they will be simple, and the disadvantages are obvious, as the transfer of data will not be subject to frequent changes, and whether or not new information is generated at the back end, the client will make a request, which will inevitably put considerable pressure on the service end, wasting bandwidth and server resources.

# Round of Questions

The long-duration inquiry is an improved version of the short-duration interview, which ensures the relative real-time nature of the message while minimizing the waste of server resources. Long rounds of inquiry are widely used in intermediates such as Nacos and Apollo Configuration Centres, and long rounds of inquiries are used in news queues of Kafka and RocketMQ.

[Nacos Configuration Center Interactive Model is push or pull?](https://mp.weixin.qq.com/s/94ftESkDoZI9gAGflLiGwg) I have described in detail the principles for the realization of Nacos long-duration inquiries, which can be seen by interested small partners.

The principle of long-duration inquiry is similar to that of a question-and-answer process. However, if there is no change in the service-end data, hold the request until the service-end data change or wait for a time lapse before returning. Upon return, the client will launch the next long round of inquiries immediately.

This time, I used the Apollo Configuration Centre to achieve a long-duration round of inquiries, using a category of `DeferredResult`, which is a mechanism for requests for a different pace provided by the Spring cover after Servlet 3.0 and is intended to delay the result.

(https://oss.javaguide.cn/github/j
