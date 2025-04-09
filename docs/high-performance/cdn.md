I don't...
Title: CDN Working Details
Category: High Performance
Head:

- Meta.
- Name: keywords
  Content: CDN, content distribution network
- Meta.
- Name: description
  Content: CDN is the distribution of static resources to many different places to achieve close access, thereby accelerating access to static resources and reducing the burden on servers and bandwidth.
  I don't...

# What's a CDN?

**CDN** fully known as Content Delivery Network, translated to mean **Content Distribution Network**.

We can untangle the content distribution network:

- Content: refers to static resources such as pictures, videos, documents, JS, CSS, HTML.
- Distribution networks: this refers to the distribution of these static resources to servers located in various geographic locations, so that close access to static resources, such as data from Beijing, can be achieved.

So, in short, **CDN is the distribution of static resources to many different places to achieve immediate access, thereby accelerating access to static resources and reducing the burden on servers and bandwidth.**

Similar to the vast warehouse transport system established in Kyoto, Kyoto Logistics has a very large number of warehouses throughout the country, and the storage network covers almost all districts and regions of the country. In that case, when a user first places an order, the goods are sent directly from the nearest warehouse to the corresponding distribution station, and then to your home by Mr. Kyung Dong.

![Warehouse Transport System](https://oss.javaguide.cn/github/javaguide/high-perform/cdn/jingdong-wuliu-cangpei.png)

You can see CDN as a special cache service at the top of the service, spread all over the country, mainly to deal with requests for static resources.

![CDN Overview](https://oss.javaguide.cn/github/javaguide/high-perform/cdn/cdn-101.png)

We've always compared full-stop speed and content distribution networks, so don't mix them up! The full-station acceleration (different cloud service providers call it ECDN, Aliyun calls it DCDN) can accelerate static and dynamic resources, while the content distribution network (CDN) is primarily aimed at **static resources**.

[Aliyun Document](https://help.aliyun.com/document_detail/64836.html) ![Aliyun CDN](https://oss.javaguide.cn/github/javaguide/high-performance/cdn/cdn-aliyun-dcdn.png)

The vast majority of companies will use CDN services in project development, but few will have their own CDN services. On the basis of cost, stability, and ease of use, it is recommended that the open CDN service provided by a specialized cloud manufacturer (e.g., Aliyun, Telecommunication Cloud, Twilight Cloud, Green Cloud) or a CDN manufacturer (e.g., Internet Board, Blue Twilight) be directly selected.

Many friends may ask: **Since it's a close visit, why not simply deploy the service in a number of different places?**

- Too costly to deploy many of the same services.
- Static resources usually occupy a larger space and are frequently accessed, and the use of servers or caches to process static resource requests is very costly and may affect the proper functioning of other system services.

The same service is deployed in many different locations (e.g., co-cities, off-sites, live-in-cities, off-site) in order to achieve high availability of systems rather than close access.

# CDN, What Does It Work For?

The following three questions are understood, and the CDN works:

1. How are static resources cached to the CDN node?
1. How can the most appropriate CDN nodes be found?
1. How can the misappropriation of static resources be prevented?

# How Are Static Resources Cached to CDN Nodes?

You can sync the source station resources to the CDN node by **preheating**. In this way, a user can request resources for the first time directly from the CDN node without returning to the source. This reduces the pressure on the source station and enhances the user experience.

If you do not preheat, the resources you visit may not be in the CDN node, at which point the CDN node will request the source station to access the resources, a process that you often say **comes back.**

> - Return: When the resource requested by the user is not on the CDN node or the cache of the resource has expired, the CDN node needs to get up-to-date resource content from the original server, and this process is the source. When a user request is sent back, the response to the request is slower than the unused CDN because there is an extra layer of CDN call process than the unused CDN.
> - Preheat: Preheat means the early cache of content on a CDN to the CDN node. This allows the user to quickly obtain these resources from the nearest CDN node without returning, thus reducing the pressure to access the source station and increasing the speed of access.

!\[CDN Returns\](https://oss.javaguide.cn/github/javaguide/high-perform/cdn
