---
title: Detailed Explanation of How CDN Works
category: High Performance
head:
  -   - meta
      - name: keywords
        content: CDN, Content Delivery Network
  -   - meta
      - name: description
        content: CDN is the distribution of static resources to multiple different places to achieve nearby access, thereby speeding up the access speed of static resources and reducing the burden on servers and bandwidth.
---

## What is CDN?

**CDN** stands for Content Delivery Network, which translates to **Content Distribution Network**.

We can break down the concept of a content distribution network:

- Content: Refers to static resources such as images, videos, documents, JS, CSS, and HTML.
- Distribution Network: Refers to distributing these static resources to servers located in different geographic locations, allowing for nearby access to static resources, such as a user in Beijing directly accessing data from the Beijing data center.

So, simply put, **CDN is the distribution of static resources to multiple different places to achieve nearby access, thereby speeding up the access speed of static resources and reducing the burden on servers and bandwidth.**

Similar to the massive warehousing and transportation system established by JD.com, JD logistics has many warehouses across the country, with a storage network covering almost all districts and counties in the country. This way, when a user places an order, the product is sent directly from the nearest warehouse to the corresponding distribution station, and then delivered to your home by JD couriers.

![JD Warehouse and Distribution System](https://oss.javaguide.cn/github/javaguide/high-performance/cdn/jingdong-wuliu-cangpei.png)

You can think of CDN as a special caching service on top of the server, distributed across the country, primarily used to handle requests for static resources.

![CDN Simplified Diagram](https://oss.javaguide.cn/github/javaguide/high-performance/cdn/cdn-101.png)

We often compare full-site acceleration with content delivery networks; do not confuse the two! Full-site acceleration (known as ECDN by Tencent Cloud and DCDN by Alibaba Cloud) can accelerate both static and dynamic resources, whereas content delivery network (CDN) mainly targets **static resources**.

![Alibaba Cloud Documentation: https://help.aliyun.com/document_detail/64836.html](https://oss.javaguide.cn/github/javaguide/high-performance/cdn/cdn-aliyun-dcdn.png)

Most companies will use CDN services in their project development, but very few will build their own CDN services. Considering cost, stability, and usability, it is advisable to choose ready-to-use CDN services offered by professional cloud vendors (such as Alibaba Cloud, Tencent Cloud, Huawei Cloud, QingCloud) or CDN providers (such as Wangsu, Blue boolean).

Many friends may ask: **Since it's about nearby access, why not deploy services directly in multiple different places?**

- The cost is too high, as multiple identical services need to be deployed.
- Static resources usually take up a lot of space and are frequently accessed. If we use servers or caches to handle static resource requests directly, it consumes a lot of system resources, potentially affecting the normal operation of other system services.

Deploying the same service in multiple different locations (such as local disaster recovery, remote disaster recovery, local active-active, remote active-active) is for achieving high availability of the system, not for nearby access.

## What is the Working Principle of CDN?

Understanding the following three questions will help you grasp the working principle of CDN:

1. How are static resources cached to CDN nodes?
1. How to find the most suitable CDN node?
1. How to prevent static resources from being misused?

### How are static resources cached to CDN nodes?

You can synchronize the origin server's resources to the CDN nodes through **warming**. This way, users requesting resources for the first time can directly retrieve them from the CDN node without needing to go back to the origin, reducing the pressure on the origin server and improving user experience.

If resources are not warmed, the requested resources may not be present at the CDN node. In this case, the CDN node will fetch the resources from the origin server, which is often referred to as **back to source**.

> - Back to Source: When the CDN node does not have the requested resource or the resource's cache has expired, the CDN node needs to retrieve the latest resource content from the original server. This process is known as back to source. When a user request undergoes back to source, it results in a response speed slower than without using CDN, as it adds an extra layer of CDN call process.
> - Warming: Warming refers to caching content in advance at the CDN nodes. This allows users to quickly retrieve resources from the nearest CDN node without going back to the origin, thereby reducing the access pressure on the origin server and improving access speed.

![CDN Back to Source](https://oss.javaguide.cn/github/javaguide/high-performance/cdn/cdn-back-to-source.png)

If there are updates to resources, you can also **refresh** them by deleting the old cached resources on the CDN node and forcing the CDN node to go back to the origin server to fetch the latest resources.

Almost all cloud providers' CDN services offer cache refresh and warming functions (the following image shows the corresponding functions provided by Alibaba Cloud CDN):

![CDN Cache Refresh and Warming](https://oss.javaguide.cn/github/javaguide/high-performance/cdn/cdn-refresh-warm-up.png)

**Hit Rate** and **Back to Source Rate** are two important indicators for measuring the quality of CDN services. The higher the hit rate, the better, and the lower the back to source rate, the better.

### How to find the most suitable CDN node?

GSLB (Global Server Load Balance) is the brain of the CDN, responsible for the cooperation among multiple CDN nodes. The most commonly used is DNS-based GSLB.

CDN uses GSLB to find the most suitable CDN node, specifically as follows:

1. The browser sends a domain name request to the DNS server;
1. The DNS server sends a request to GSLB based on the CNAME (Canonical Name) alias record;
1. GSLB returns the address of the best-performing (usually the nearest to the request address) CDN node (edge server, the actual caching location) to the browser;
1. The browser directly accesses the specified CDN node.

![CDN Principle Diagram](https://oss.javaguide.cn/github/javaguide/high-performance/cdn/cdn-overview.png)

To facilitate understanding, the above diagram simplifies certain aspects. Internally, GSLB can be viewed as a combination of a CDN-specific DNS server and a load-balancing system. The CDN-specific DNS server returns the IP address of the load-balancing system to the browser, which then uses this IP address to request the load-balancing system to find the corresponding CDN node.

**How does GSLB choose the most suitable CDN node?** GSLB comprehensively judges which CDN node’s address to return based on metrics such as the request's IP address, the status of the CDN node (such as load status, performance, response time, bandwidth), etc.

### How to prevent misuse of resources?

If our resources are illegally accessed or scraped by other users or websites, it could result in significant expenses.

The most common and straightforward solution to this problem is to set up **Referer anti-hotlinking**. Specifically, this limits requests based on the Referer field in the HTTP request headers. By utilizing the Referer field, we can determine the originating website address of the current request and ascertain whether it comes from a legitimate website.

Almost all CDN service providers offer this basic anti-hotlinking mechanism.

![Tencent Cloud CDN Referer Anti-Hotlinking Configuration](https://oss.javaguide.cn/github/javaguide/high-performance/cdn/cnd-tencent-cloud-anti-theft.png)

However, if the site's anti-hotlinking configuration allows a blank Referer, it is possible to bypass the anti-hotlinking by hiding the Referer.

Usually, we combine other mechanisms to ensure the static resources are not misused. A commonly used mechanism is **timestamp anti-hotlinking**. Compared to the simple Referer-based approach, **timestamp anti-hotlinking** offers stronger security. The encrypted URL in timestamp anti-hotlinking is time-sensitive, and once it expires, access is no longer permitted.

The URL for timestamp anti-hotlinking typically includes two parameters: a signature string and an expiration time. The signature string is generally obtained by executing an MD5 hash on a user-defined encrypted string, request path, and expiration time.

Example URL for timestamp anti-hotlinking:

```plain
http://cdn.wangsu.com/4/123.mp3?wsSecret=79aead3bd7b5db4adeffb93a010298b5&wsTime=1601026312
```

- `wsSecret`: Signature string.
- `wsTime`: Expiration time.

![](https://oss.javaguide.cn/github/javaguide/high-performance/cdn/timestamp-anti-theft.png)

The implementation of timestamp anti-hotlinking is relatively simple and highly reliable, making it a recommended option. Additionally, most CDN service providers offer plug-and-play timestamp anti-hotlinking mechanisms.

![Qiniu Cloud Timestamp Anti-Hotlinking Configuration](https://oss.javaguide.cn/github/javaguide/high-performance/cdn/qiniuyun-timestamp-anti-theft.png)

In addition to Referer anti-hotlinking and timestamp anti-hotlinking, you can also utilize IP black and white list configurations, IP access rate limiting configurations, and other mechanisms to prevent misuse.

## Summary

- CDN is the distribution of static resources to multiple different places to achieve nearby access, thereby speeding up the access speed of static resources and reducing the burden on servers and bandwidth.
- Considering cost, stability, and usability, it is advisable to choose ready-to-use CDN services offered by professional cloud vendors (such as Alibaba Cloud, Tencent Cloud, Huawei Cloud, QingCloud) or CDN providers (such as Wangsu, Blue boolean).
- GSLB (Global Server Load Balance) is the brain of the CDN, responsible for the cooperation among multiple CDN nodes. The most common implementation is DNS-based GSLB. CDN utilizes GSLB to find the most suitable CDN node.
- To prevent the misuse of static resources, we can use **Referer anti-hotlinking** combined with **timestamp anti-hotlinking**.

## References

- Timestamp Anti-Hotlinking - Qiniu Cloud CDN: <https://developer.qiniu.com/fusion/kb/1670/timestamp-hotlinking-prevention>
- What on Earth is CDN? A Clear Explanation: <https://mp.weixin.qq.com/s/Pp0C8ALUXsmYCUkM5QnkQw>
- "Understanding HTTP Protocol" - 37 | CDN: Accelerating Our Network Services: <http://gk.link/a/11yOG>

<!-- @include: @article-footer.snippet.md -->
