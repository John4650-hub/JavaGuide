I don't...
Title: Java High-Quality Open Source Type
Category: Open Source Project
icon:
I don't...

# Code Quality

- [Lombok](https://github.com/rzwitserloot/lombok): A powerful repository that can simplify Java code. By using Lombok annotations, we can automatically generate commonly used code logic, such as `getter`, `setter`, `equals`, `hashCode`, `toString` methods, as well as builders, log variables, etc.
- [Guava](https://github.com/google/guava "guava"): Google developed a powerful core library that expands Java's standard library functionality. It provides a number of useful tools and aggregation types, such as `Multimap` (multi-value map), `Multiset` (multi-set), `BiMap` (bi-directional map) and flexible collections, as well as graphic processing libraries and co-production tools. Guava also supports many practical functions such as I/O operations, hashing algorithms, string processing, caches, etc.
- [Hutool](https://github.com/looly/hutool "hutool"): A comprehensive and user-friendly Java tool bank designed to simplify the development of tasks through minimal reliance. It covers a number of functional features, such as file operations, caches, encryption/decryption, logs, and file operations.

# Problem Sorting and Performance Optimization

- [Arthas](https://github.com/alibaba/arthas "arthas"): Alibaba's open-source Java diagnostic tool for real-time monitoring and diagnosis of Java applications. It provides a wealth of commands and functions to analyze the performance of applications, including resource consumption and load time during start-up.
- [Async Profiler](https://github.com/async-profiler/async-profiler): The Java performance analysis tool, which collects and analyzes performance data for applications.
- [Spring Boot Startup Report](https://github.com/maciejwalkowiak/spring-boot-startup-report): A tool for generating Spring Boot start-up reports. It provides detailed start-up information, including loading time for each bean, time-consuming automatic configuration, etc., to help you analyze and optimize the start-up process.
- [Spring Startup Analyzer](https://github.com/linyimin0812/spring-startup-analyzer/blob/main/README_ZH.md): Collects Spring application startup data and generates interactive analysis (HTML) to analyze Spring application startup bottlenecks to support Spring Beans in initiating different steps to reduce the optimization of Spring application start time. UI Reference: [Spring Boot Startup Report](https://github.com/maciejwalkowiak/spring-boot-startup-report)

# Document Processing

## Document Parsing

- [Tika](https://github.com/apache/tika): Apache Tika toolkit can detect and extract metadata and text content from over 1,000 different file types (e.g., PPT, XLS, and PDF).

## Excel

- [EasyExcel](https://github.com/alibaba/easyexcel): Java processing Excel tool for fast and simple avoidance of OOM. However, the project is no longer maintained and has moved to [FastExcel](https://github.com/fast-excel/fastexcel).
- [Excel Spring Boot Starter](https://github.com/pig-mesh/excel-spring-boot-starter): Spring Boot Starter, based on FastExcel, to simplify Excel reading and writing.
- [Excel Streaming Reader](https://github.com/monitorjbl/excel-streaming-reader): Excel flow-class-style reading tool (only for XLSX files), based on Apache POI encapsulation while retaining the syntax of the standard POI API.
- [MyExcel](https://github.com/liaochong/myexcel): A toolkit that collects multiple functions such as import, export, encryption, etc.

## Word

- [poi-tl](https://github.com/Sayi/poi-tl): Word Template Engine based on Apache POI can generate Word documents based on Word templates and data.

## JSON

- [JsonPath](https://github.com/json-path/JsonPath): Tool bank for processing JSON data.

## PDF

For simple PDF creation needs, OpenPDF is a good choice; it's open and free, with an easy-to-use API. For complex scenarios that require operations such as resolution, conversion, and extraction of text, you can choose Apache PDFBox. Of course, if you don't mind LGPL permission, there's iText.

- [x-easypdf](https://gitee.com/dromara/x-easypdf): A framework (based on PDFBox/FOP) for the construction of PDF-based building blocks to support PDF export and editing, suitable for simple PDF document generation scenarios.
- [iText](https://github.com/itext/itext7): A Java library for creating, editing, and enhancing PDF documents. The iText 7 Community version uses AGPL licenses, which are required if your project is a closed-source business project. iText 5 is still under LGPL license that can
