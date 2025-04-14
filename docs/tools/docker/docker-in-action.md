---
title: Docker Practice
category: Development Tools
tag:
  - Docker
---

## Introduction to Docker

Before we begin, let's briefly introduce Docker. For more concepts related to Docker, you can refer to the previous article [Summary of Core Docker Concepts](./docker-intro.md).

### What is Docker?

To be honest, it's not easy to explain what Docker is. Below, I will clarify what Docker is through four points.

- Docker is the world's leading software container platform, developed using **Go language**.
- Docker can automatically execute repetitive tasks, such as setting up and configuring development environments, thus freeing developers.
- Users can easily create and use containers to place their applications. Containers can also be versioned, copied, shared, and modified, just like managing regular code.
- Docker can **encapsulate and isolate processes, which is a virtualization technology at the operating system level.** Since isolated processes are independent of the host and other isolated processes, they are also referred to as containers.

Official website: <https://www.docker.com/>.

![Understanding Containers](https://oss.javaguide.cn/github/javaguide/tools/docker/container.png)

### Why Use Docker?

Docker allows developers to package their applications and dependencies into a lightweight, portable container, which can then be deployed on any popular Linux machine, achieving virtualization.

Containers use a complete sandbox mechanism, with no interfaces between them (similar to apps on an iPhone). More importantly, the performance overhead of containers is extremely low.

In traditional development processes, our projects often require environments like MySQL, Redis, FastDFS, etc. These environments need to be manually downloaded and configured, making the installation and configuration process extremely complex, and operations vary across different systems.

The emergence of Docker perfectly solves this problem. We can install software environments like MySQL and Redis in containers, separating applications from the environment architecture. Its advantages include:

1. A consistent runtime environment that can be migrated more easily.
2. Encapsulation and isolation of processes, allowing containers to operate independently and utilize system resources more efficiently.
3. The ability to replicate multiple consistent containers through images.

Additionally, the open-source book [“Docker from Beginner to Practice”](https://yeasy.gitbook.io/docker_practice/introduction/why) has already provided reasons for using Docker.

![](https://oss.javaguide.cn/github/javaguide/tools/docker/20210412220015698.png)

## Installing Docker

### Windows

Next, let's install Docker, using Windows as an example. Visit Docker's official website:

![Install Docker](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-install-windows.png)

Then click `Get Started`:

![Install Docker](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-install-windows-download.png)

Click `Download for Windows` to start the download.

If your computer is running `Windows 10 64-bit Professional`, you need to enable `Hyper-V` before installing Docker. The steps to enable it are as follows. Open the Control Panel and select Programs:

![Enable Hyper-V](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-windows-hyperv.png)

Click `Turn Windows features on or off`:

![Enable Hyper-V](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-windows-hyperv-enable.png)

Check `Hyper-V` and click OK:

![Enable Hyper-V](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-windows-hyperv-check.png)

After completing the changes, you need to restart your computer.

Once `Hyper-V` is enabled, we can proceed to install Docker. Open the installer, wait a moment, and click `Ok`:

![Install Docker](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-windows-hyperv-install.png)

After the installation is complete, we still need to restart the computer. After restarting, if prompted with the following message:

![Install Docker](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-windows-hyperv-wsl2.png)

It is asking whether we want to use WSL2, which is a Linux subsystem based on Windows. Here we can cancel, and it will use the `Hyper-V` virtual machine we selected earlier.

Since this is a graphical interface operation, I will not introduce the specific usage of Docker Desktop here.

### Mac

You can install it directly using Homebrew:

```shell
brew install --cask docker
```

### Linux

Now let's see how to install Docker on Linux, taking CentOS 7 as an example.

In testing or development environments, Docker provides a convenient installation script to simplify the installation process. Executing this script will automatically prepare everything and install the stable version of Docker on the system.

```shell
curl -fsSL get.docker.com -o get-docker.sh
```

```shell
sh get-docker.sh --