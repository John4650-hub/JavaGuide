---
title: Summary of Core Git Concepts
category: Development Tools
tag:
  - Git
---

## Version Control

### What is Version Control

Version control is a system that records changes to one or more files so that specific versions can be reviewed in the future. You can apply version control to any type of file, not just project source code.

### Why Use Version Control

With version control, you can revert a file to a previous state, or even roll back an entire project to a specific point in time. You can compare the details of file changes, identify who last modified which part, and find out the reasons behind strange issues or who reported a particular bug at what time.

### Local Version Control Systems

Many people are accustomed to saving different versions by copying the entire project directory, perhaps renaming it with a backup timestamp for distinction. The only advantage of this method is its simplicity, but it is prone to errors. Sometimes, it can lead to confusion about the working directory, and you might accidentally write to or overwrite unintended files.

To address this issue, various local version control systems were developed long ago, most of which use a simple database to record the differences in file updates.

![Local Version Control Systems](https://oss.javaguide.cn/github/javaguide/tools/git/%E6%9C%AC%E5%9C%B0%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6%E7%B3%BB%E7%BB%9F.png)

### Centralized Version Control Systems

Next, people encountered the problem of how to enable developers on different systems to collaborate. Thus, Centralized Version Control Systems (CVCS) emerged.

Centralized version control systems have a single centralized server that stores all file revision versions, and collaborators connect to this server via clients to retrieve the latest files or submit updates.

![Centralized Version Control Systems](https://oss.javaguide.cn/github/javaguide/tools/git/%E9%9B%86%E4%B8%AD%E5%8C%96%E7%9A%84%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6%E7%B3%BB%E7%BB%9F.png)

While this approach resolves the issue of local version control systems not allowing collaboration across different systems, it still has the following problems:

- **Single Point of Failure:** If the central server goes down, others cannot use it; if the central database disk fails and there is no backup, you will lose all data. Local version control systems have a similar issue; as long as the entire project's history is stored in a single location, there is a risk of losing all historical updates.
- **Requires Internet Connection:** It is affected by network conditions and bandwidth.

### Distributed Version Control Systems

Thus, Distributed Version Control Systems (DVCS) were introduced. Git is a typical example of a distributed version control system.

In these systems, the client does not just retrieve the latest version of the file snapshot but mirrors the entire code repository. This way, if any collaborative server fails, any mirrored local repository can be used to recover. Each clone operation is essentially a complete backup of the code repository.

![Distributed Version Control Systems](https://oss.javaguide.cn/github/javaguide/tools/git/%E5%88%86%E5%B8%83%E5%BC%8F%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6%E7%B3%BB%E7%BB%9F.png)

Distributed version control systems can work without an internet connection because each person's computer has a complete version library. When you modify a file, you only need to push your changes to others. However, in practice, changes are rarely pushed directly; instead, a server acts as a "central server" to facilitate the "exchange" of modifications. Without it, everyone can still work, but exchanging changes becomes less convenient.

The advantages of distributed version control systems go beyond just not needing an internet connection; we will also see Git's powerful branch management and other features later.

## Understanding Git

### Brief History of Git

The Linux kernel project team used the distributed version control system BitKeeper to manage and maintain code. However, the commercial company that developed BitKeeper ended its collaboration with the Linux kernel open-source community, revoking their right to use BitKeeper for free. Based on the lessons learned from using BitKeeper, the Linux open-source community (especially Linus Torvalds, the creator of Linux) developed its own version control system, making many improvements over the previous system.

### Key Differences Between Git and Other Version Control Systems

Git differs significantly from other version control systems in how it saves and handles various information, even though the command forms are quite similar. Understanding these differences will help prevent confusion during use.

Here, we mainly discuss
