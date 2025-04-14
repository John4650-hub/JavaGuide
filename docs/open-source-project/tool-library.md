---
title: Java High-Quality Open Source Utility Classes
category: Open Source Projects
icon: codelibrary-fill
---

## Code Quality

- [Lombok](https://github.com/rzwitserloot/lombok): A powerful utility library that simplifies Java code. By using Lombok's annotations, we can automatically generate common code logic such as `getter`, `setter`, `equals`, `hashCode`, `toString` methods, as well as constructors, log variables, and more.
- [Guava](https://github.com/google/guava "guava"): A powerful core library developed by Google that extends the functionality of the Java standard library. It provides many useful utility classes and collection types, such as `Multimap`, `Multiset`, `BiMap`, and immutable collections. Additionally, it includes libraries for graphical processing and concurrency utilities. Guava also supports I/O operations, hash algorithms, string processing, caching, and various other practical functions.
- [Hutool](https://github.com/looly/hutool "hutool"): A comprehensive and user-friendly Java utility library designed to simplify development tasks with minimal dependencies. It encapsulates many practical functions, such as file operations, caching, encryption/decryption, logging, and file manipulation.

## Problem Diagnosis and Performance Optimization

- [Arthas](https://github.com/alibaba/arthas "arthas"): An open-source Java diagnostic tool from Alibaba that can monitor and diagnose Java applications in real-time. It provides a rich set of commands and functions to analyze performance issues in applications, including resource consumption and loading time during startup.
- [Async Profiler](https://github.com/async-profiler/async-profiler): A low-overhead asynchronous Java performance profiling tool used to collect and analyze application performance data.
- [Spring Boot Startup Report](https://github.com/maciejwalkowiak/spring-boot-startup-report): A tool used to generate startup reports for Spring Boot applications. It provides detailed information about the startup process, including loading times for each bean and duration of auto-configuration, helping you analyze and optimize the startup process.
- [Spring Startup Analyzer](https://github.com/linyimin0812/spring-startup-analyzer/blob/main/README_ZH.md): Captures startup data of Spring applications and generates interactive analysis reports (HTML) to analyze bottlenecks during the Spring application startup, supporting asynchronous initialization of Spring Beans to reduce startup time. The UI is inspired by the [Spring Boot Startup Report](https://github.com/maciejwalkowiak/spring-boot-startup-report).

## Document Processing

### Document Parsing

- [Tika](https://github.com/apache/tika): The Apache Tika toolkit can detect and extract metadata and text content from over a thousand different file types (such as PPT, XLS, and PDF).

### Excel

- [EasyExcel](https://github.com/alibaba/easyexcel): A fast and simple Java tool for processing Excel that avoids OOM errors. However, this project is no longer maintained and has been migrated to [FastExcel](https://github.com/fast-excel/fastexcel).
- [Excel Spring Boot Starter](https://github.com/pig-mesh/excel-spring-boot-starter): A Spring Boot Starter based on FastExcel that simplifies reading and writing Excel files.
- [Excel Streaming Reader](https://github.com/monitorjbl/excel-streaming-reader): An Excel streaming utility that supports reading XLSX files, based on Apache POI, while retaining the standard POI API syntax.
- [MyExcel](https://github.com/liaochong/myexcel): A toolkit that integrates multiple functions such as importing, exporting, and encrypting Excel files.

### Word

- [poi-tl](https://github.com/Sayi/poi-tl): A Word template engine based on Apache POI that can generate Word documents based on Word templates and data, providing a WYSIWYG experience!

### JSON

- [JsonPath](https://github.com/json-path/JsonPath): A utility library for processing JSON data.

### PDF

For simple PDF creation needs, OpenPDF is a great choice as it is open-source and free with a simple API. For complex scenarios requiring parsing, conversion, and text extraction operations, Apache PDFBox is recommended. If you don't mind LGPL licensing for complex scenarios, iText can also be considered.

- [x-easypdf](https://gitee.com/dromara/x-easypdf): A framework for building PDFs in a modular way (based on pdfbox/fop) that supports PDF exporting and editing, suitable for simple PDF document generation scenarios.
- [iText](https://github.com/itext/itext7): A Java library for creating, editing, and enhancing PDF documents. iText 7 community edition is licensed under AGPL; if your project is a closed-source commercial project, you will need to purchase a commercial license. iText 5 is still LGPL licensed, allowing free commercial use but has stopped maintenance.
- [OpenPDF](https://github.com/LibrePDF/OpenPDF): Completely open-source and free (LGPL/MPL dual license), based on a fork of iText, it can serve as an alternative to iText, being simple and easy to use, but with fewer features compared to iText (sufficient for most scenarios).
- [Apache PDFBox](https://github.com/apache/pdfbox): Completely open-source and free (Apache License), highly powerful, supporting PDF creation, parsing, conversion, and text extraction. However, due to its extensive features, the API design is relatively complex, which may increase the learning difficulty.
- [FOP](https://xmlgraphics.apache.org/fop/): Apache FOP is used to render XSL-FO (Extensible Stylesheet Language Formatting Objects) into various output formats, with PDF being the most common.

## Image Processing

- [Thumbnailator](https://github.com/coobird/thumbnailator): An image processing utility library primarily for resizing images, adding watermarks, rotating images, adjusting image sizes, and area cropping.
- [Imglib](https://github.com/nackily/imglib): A lightweight Java image processing library aimed at simplifying common image processing tasks, primarily offering three capabilities: image collection, image processing (based on Thumbnailator), aggregation, and splitting.

## Captcha

- [EasyCaptcha](https://gitee.com/whvse/EasyCaptcha): A Java graphical captcha supporting types such as gif, Chinese characters, and arithmetic, usable in Java Web, JavaSE, and other projects.
- [AJ-Captcha](https://gitee.com/anji-plus/captcha): Behavioral captcha (sliding puzzle, text selection) with frontend and backend (Java) interaction.
- [tianai-captcha](https://gitee.com/tianai/tianai-captcha): A visually appealing and easy-to-use sliding block captcha.

## SMS & Email

- [SMS4J](https://github.com/dromara/SMS4J): An SMS aggregation framework that simplifies the process of integrating multiple SMS SDKs.
- [Simple Java Mail](https://github.com/bbottema/simple-java-mail): The simplest lightweight Java email library capable of sending complex emails.

## Online Payments

- [Jeepay](https://gitee.com/jeequan/jeepay): An open-source payment system suitable for internet enterprises that has implemented interfaces for transactions, refunds, transfers, and profit sharing, supporting both service provider special merchant and ordinary merchant interfaces. It has integrated official interfaces for WeChat, Alipay, and UnionPay, supporting aggregated code payments.
- [YunGouOS-PAY-SDK](https://gitee.com/YunGouOS/YunGouOS-PAY-SDK): YunGouOS WeChat payment interface, official WeChat personal payment interface, non-QR code collection, and non-fourth-party clearance. Individual users can submit materials to open WeChat payment merchants for integration.
- [IJPay](https://gitee.com/javen205/IJPay): A payment aggregation library that makes payments accessible and wraps common payment methods such as WeChat Pay, QQ Pay, Alipay, JD Pay, UnionPay, and PayPal with various commonly used interfaces.

## Others

- [oshi](https://github.com/oshi/oshi "oshi"): A library providing (native) operating system and hardware information for the Java language, based on JNA.
- [ip2region](https://github.com/lionsoul2014/ip2region): The most flexible IP address query library mapping IPs to regions, offering three query algorithms: Binary, B-Tree, and pure memory, so mom no longer needs to worry about my IP address being located.
- [agrona](https://github.com/real-logic/agrona): High-performance Java data structures (`Buffers`, `Lists`, `Maps`, `Scalable Timer Wheel`, etc.) and utility methods.
