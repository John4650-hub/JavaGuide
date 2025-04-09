I don't...
Title: Git Core Concept Summary
Category: Development tools
Tag:

- Git.
  I don't...

# Version control

What's version control?

Version control is a system for recording changes in the content of one or more documents in order to provide future access to specific revisions. With the exception of the project source code, you can have any type of file under version control.

# Why is version control important?

With it, you can trace a document back to the status quo ante, or even the entire project back to a certain point in time. You can compare the details of the changes in the document, find out who last modified it, and discover the reasons that led to a strange problem, as well as when a functional defect was reported.

## Local version control system

Many people are accustomed to preserving different versions by copying the entire project catalog, perhaps with a different name and backup time. The only benefit of doing so is simplicity, but it is particularly easy to make mistakes. Sometimes the working directory gets confused, and the document is inadvertently miswritten or overwritten.

To address this problem, a number of local version control systems have long been developed, most of them using some simple database to record document updates.

![Local version control system](https://oss.javaguide.cn/github/javaguide/tools/git/%E6%9C%AC%E5%9C%B0%E7%89%88%E6%9C%AC%E6%8A%80%E8%83%BD%E7%9A%84%E7%8A%B6%E6%80%81%E5%9B%BE%E7%89%87.png)

## A centralized version control system

Then another question arises: how can developers work together on different systems? As a result, a centralized version control system (CVCS) was created.

The centralized version control system has a single centrally managed server that saves all the revised versions of documents, while the people working together are connected to the server through the client, extracting up-to-date documents or submitting updates.

![Centralized version control system](https://oss.javaguide.cn/github/javaguide/tools/git/%E9%9B%86%E4%B8%AD%E7%9A%84%E7%8E%AF%E5%A2%83%E7%9A%84%E7%8A%B6%E6%80%81%E5%9B%BE%E7%89%87.png)

While this has addressed the problem of local version control systems that do not allow developers on different systems to work together, the following problems remain:

- **Single point of failure:** Central server failure makes it unavailable to others; if the central database disk is damaged and not backed up, you will lose all data. Similar problems exist with the local version control system, which risks losing all historical updates as long as the historical records of the entire project are kept in a single location.
- **The need to network to work:** Affected by the state of the network and bandwidth.

## Distributed version control system

So the distributed version control system (DVCS) came into being. Git is a typical distributed version control system.

In such systems, the client does not only extract the latest version of the file snapshot but rather completes the code repository with a geo-image. As a result, if any one of the co-working servers malfunctions, it can be restored to a local repository with any mirror. Each cloning operation is actually a complete backup of the code repository.

![Distributed version control system](https://oss.javaguide.cn/github/javaguide/tools/%E5%88%86%E5%8C%BA%E7%9A%84%E7%8E%AF%E5%A2%83%E7%9A%84%E7%8A%B6%E6%80%81%E5%9B%BE%E7%89%87.png)

The distributed version control system can work without a network because everyone's computer is a complete version library. When you modify a file, you just have to send your own changes to someone else. However, when a distributed version control system is used in practice, it is rarely done directly by pushing changes, but by using a “central server.” The server's function is simply to facilitate the “exchange” of changes; without it, everyone works, but it is not convenient to exchange changes.

The advantage of distributed version control systems is not only that there is no need to network, but also that we will see such functions as Git's extremely powerful branch management.

# Know Git

## Git: A short history

The Linux kernel project team then used the distributed version control system BitKeeper to manage and maintain the code. However, the business company that developed BitKeeper ended its partnership with the Linux kernel open-source community by withdrawing the power of the Linux kernel community to use BitKeeper free of charge. The Linux Open Source Community (especially Linus Torvalds,
