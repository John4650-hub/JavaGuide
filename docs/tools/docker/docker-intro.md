I don't...
Title: Docker Core Concept Summary
Category: Development tools
Tag:
Docker.
I don't...

This paper provides only a more detailed description of the Docker concept and does not address certain common operations and commands like the Docker environment.

# The container description

**Docker is the world's leading software container platform**, so to understand the Docker concept we have to start with the container.

What's a container?

Let's start with the more official explanation of the container.

**One sentence summarizes the packaging: the packaging consists of packaging the software into a standardized module for development, delivery, and deployment.**

- **The container mirrors are a light, implementable, stand-alone software package** and contain all the elements required to run the software: code, runtime environment, system tools, system libraries, and settings.
- **Containerization software applies to applications based on Linux and Windows and can be consistently operated in any environment.**
- **Packagings provide software independence** to protect them from external environmental differences (e.g., differences in the development and preview environments), thus helping to reduce conflicts between teams when operating different software on the same infrastructure.

Let's see the more common explanation for the container.

If you need to describe containers in a popular way, I think they are a place where things are stored, like school bags with stationery, closets with clothes, and shoe racks with shoes. What we're talking about is that what we're talking about in the container is probably more in favor of applications such as websites, programs, and even the system environment.

![Knower packagings](https://oss.javaguide.cn/github/javaguide/tools/docker/container.png)

## Graphic physics machine, virtual machine, and container

The comparison between virtual machines and containers is described in detail at the end, where only images from the Internet deepen understanding of physical machines, virtual machines, and containers (the images below are from the network).

**The physics machine:**

![Physics machine](https://oss.javaguide.cn/github/javaguide/tools/docker/%E7%89%A9%E7%90%86%E6%9C%BA%E5%9B%BE%E8%A3.jpeg)

**View machine:**

![View machine](https://oss.javaguide.cn/github/javaguide/tools/docker/%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%9B%BE%E8%A3.jpeg)

**Container:**

![Container](https://oss.javaguide.cn/javaguide/image-202111104003678.png)

With these three abstract images above, we can sum up by analogy: **The virtualization of the packaging is the operating system, not the hardware, and the sharing of the same operating system resources between the packagings. Virtual machine technology operates a complete operating system on a virtual hardware set. The containment level of the container would therefore be slightly lower.**

## Container VS Virtual Machine

Whenever we talk about containers, we have to compare them with virtual machines. For my part, it does not matter who replaces them, but they can coexist in harmony.

In simple terms: **Packagings and virtual machines have similar resource segregation and distribution advantages, but their functions differ, as packagings are virtualized by operating systems rather than hardware, making them easier to transplant and more efficient.**

The traditional virtual machine technology is the virtualization of a complete operating system on which the application process required is re-engineered; the application process in the container is directly in the host's kernel, where there is no kernel of its own and there is no hardware virtualization. Packagings are thus lighter than traditional virtual machines.

![Comparison of containers and virtual machines](https://oss.javaguide.cn/javaguide/2e2b95eebf60b6d03f6c1476f4d7c697.png)

**Comparison of containers and virtual machines**:

![Comparison of containers and virtual machines](https://oss.javaguide.cn/javaguide/4ef8691d67eb1eb53217099d0a691eb5.png)

- The packaging is an application-level abstract, which is used to package the code and depend on resources. Multiple packagings can be operated on the same machine, sharing the core in the operating system, but each operates as a separate process in the user space. Compared to virtual machines, **packagings occupy less space** (the size of the container mirrors is usually only a few dozen megabytes), **startup can be completed in a moment**.

- Virtual Machine (VM) is an abstract layer of physical hardware used to transform a server into a multi-server. The management program allows multiple VMs to run on a machine. Each VM contains a set of operating systems, one or more applications, necessary binary files, and library resources, and therefore **occupies a large amount of space**. And VM **starts very slowly.**

Through the Docker network, we know so much
