---
title: Distributed ID Design Guide
category: Distributed
---

::: tip

I came across an article by Baidu Geek discussing the design of distributed IDs in specific scenarios, and I found it quite insightful. Therefore, I have organized some of the content from this article here. You can find the original article here: [Technical Principles and Project Practice of Distributed ID Generation Service](https://mp.weixin.qq.com/s/bFDLb6U6EgI-DvCdLTq_QA).

:::

Most distributed ID generation services available online generally focus on technical principles and rarely discuss how to choose ID generation services based on specific business scenarios.

This article explores the specific requirements for IDs in various business scenarios.

## Scenario 1: Order System

When we shop in a mall, we often use a QR code for payment, and the order number generated upon placing an order, along with the coupon codes and product exchange codes, are frequently used identifiers in online shopping. So why are some order numbers so long while others are only a few digits? Some order numbers clearly indicate the date, while others seem meaningless. Below, we analyze the specific implementations of ID services in different scenarios within the order system.

### 1. One-Code Payment

The common one-code payment refers to a QR code that can be scanned using Alipay or WeChat for payment.

The essence of a QR code is a string. The aggregation code is essentially a link. Users can directly scan a code with Alipay or WeChat to make payments without worrying about scanning the wrong payment code, which greatly reduces the time taken for users to complete the payment.

The implementation principle is that when a customer scans the code with the app, the website backend determines the customer's scanning environment (WeChat, Alipay, QQ Wallet, JD Pay, UnionPay, etc.).

The principle for determining the scanning environment is based on the HTTP header of the browser that opens the link. Any browser that opens an HTTP link will have a User-Agent (UA) information in the request header.

The UA is a special string header that allows the server to identify the customer's operating system and version, CPU type, browser and version, browser rendering engine, browser language, browser plugins, and much more.

The names of payment products vary across different channels, so it is essential to carefully review the API documentation for each payment product.

1. WeChat Pay: JSAPI Payment
1. Alipay: Mobile Website Payment
1. QQ Wallet: Public Account Payment

Essentially, they all implement HTML5 payment within the app's built-in browser.

![Library Member Payment Example](https://oss.javaguide.cn/github/javaguide/system-design/distributed-system/distributed-id-design-pay-one-card.png)

The development team at the library has optimized this approach. They dynamically generate a one-code payment QR code that is pre-bound to the product information and price selected by the user, updating dynamically based on the user's selection. This not only supports multi-platform payment initiation but also allows users to complete order payments without selecting products or entering amounts, providing a smooth experience. Only after the user scans the code does the server obtain the user's UID from the frontend, combine it with the product information bound to the QR code, truly generate the order, and send the payment information to third parties (QQ, WeChat, Alipay), which then generate the payment order and push it to the user's device to initiate payment.

Unlike fixed one-code payments, the library's application uses dynamic QR codes, where the QR code is essentially a short URL, and the ID service provides a unique identifier for the short URL. The unique short URL maps to the ID bound to the product's order information, deeply integrating technology and business, shortening the payment process, and enhancing the user's payment experience.

### 2. Order Number

In actual business processes, the order number serves as a unique identifier for an order and generally fulfills the following business scenarios:

1. Users encounter issues with their orders and need to contact customer service for assistance.
1. Operations on the order, such as offline payments and order verification.
1. Internal order processing and follow-up for placing, modifying, completing, canceling, and after-sales orders.

Often, when searching for order-related information, the order ID is used as a unique identifier, which is determined by the uniqueness of the order number generation rules. From a technical perspective, in addition to the necessary characteristics of the ID service, the design of the order number needs to reflect several features:

**(1) Information Security**

The numbering should not reveal the company's operational status, such as daily sales, company serial numbers, or any commercial information and user privacy information like phone numbers and ID numbers. Additionally, there should not be any obvious overall patterns (local patterns are acceptable), and modifying a single character should not allow access to another order's information.

This is akin to the generation rules for student numbers during our college entrance examination, which must not be sequential; otherwise, one could easily search for another student's scores based on the order.

\*\*(2) Partial
