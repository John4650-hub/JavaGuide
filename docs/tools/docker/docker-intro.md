---
title: Summary of Core Concepts of Docker
category: Development Tools
tag:
  - Docker
---

This article provides a detailed introduction to concepts of Docker, without covering the installation of the Docker environment or some common operations and commands.

## Introduction to Containers

**Docker is the world's leading software container platform**, so to understand Docker concepts, we must start with containers.

### What is a Container?

#### Let's first take a look at the official explanation of containers

**In one sentence: a container is a standardized unit that packages software for development, delivery, and deployment.**

- **A container image is a lightweight, executable, standalone software package** that includes everything needed to run the software: code, runtime environment, system tools, system libraries, and settings.
- **Containerized software runs consistently in any environment, applicable to applications based on Linux and Windows.**
- **Containers grant software independence**, shielding it from external environmental differences (such as differences between development and staging environments), thus helping to reduce conflicts when different software runs on the same infrastructure among teams.

#### Now let's look at a more colloquial explanation of containers

To describe containers in simple terms, I think of a container as a place to store things, just like a backpack can hold various stationery, a wardrobe can hold different clothes, and a shoe rack can hold different shoes. The containers we refer to now might store more application-related items, such as websites, programs, or even system environments.

![Understanding Containers](https://oss.javaguide.cn/github/javaguide/tools/docker/container.png)

### Visual Comparison of Physical Machines, Virtual Machines, and Containers

A detailed comparison between virtual machines and containers will be covered later, but here we use images found online to deepen everyone's understanding of the three: physical machines, virtual machines, and containers (the images below are sourced from the internet).

**Physical Machine:**

![Physical Machine](https://oss.javaguide.cn/github/javaguide/tools/docker/%E7%89%A9%E7%90%86%E6%9C%BA%E5%9B%BE%E8%A7%A3.jpeg)

**Virtual Machine:**

![Virtual Machine](https://oss.javaguide.cn/github/javaguide/tools/docker/%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%9B%BE%E8%A7%A3.jpeg)

**Container:**

![](https://oss.javaguide.cn/javaguide/image-20211110104003678.png)

From the above three abstract images, we can summarize through analogy: **containers virtualize the operating system instead of the hardware, and containers share the same set of operating system resources. Virtual machine technology virtualizes a set of hardware and runs a complete operating system on it. Therefore, the isolation level of containers is somewhat lower.**

### Containers VS Virtual Machines

Whenever we talk about containers, we cannot avoid comparing them with virtual machines. In my opinion, it's not about which one will replace the other; both can coexist harmoniously.

In simple terms: **containers and virtual machines have similar advantages in resource isolation and allocation, but their functionalities differ; containers virtualize the operating system rather than hardware, making them easier to port and more efficient.**

Traditional virtual machine technology virtualizes a set of hardware and runs a complete operating system on it, and then runs the needed application processes on that operating system; on the other hand, containerized application processes run directly on the host's kernel, with containers not possessing their kernel, nor performing hardware virtualization. Thus, containers are lighter than traditional virtual machines.

![](https://oss.javaguide.cn/javaguide/2e2b95eebf60b6d03f6c1476f4d7c697.png)

**Comparison of Containers and Virtual Machines**:

![](https://oss.javaguide.cn/javaguide/4ef8691d67eb1eb53217099d0a691eb5.png)

- A container is an application-layer abstraction used to package code and its dependencies together. Multiple containers can run on the same machine, sharing the operating system kernel, but each runs as an independent process in user space. Compared to virtual machines, **containers take up less space** (container images are usually only a few dozen megabytes) and **can start almost instantly**.

- A virtual machine (VM) is a physical hardware-layer abstraction, allowing one server to become multiple servers. The hypervisor allows multiple VMs to run on the same machine. Each VM contains a complete operating system, one or more applications, and the necessary binaries and libraries, thus **occupying a large amount of space**. Additionally, VMs have **slower startup times**.

From the Docker official website, we learned many advantages of Docker. However, it is unnecessary to completely deny virtual machine technology since both have different use cases. **Virtual machines excel at completely isolating the entire runtime environment**. For instance, cloud service providers often use virtual machine technology to isolate different users. Conversely, **Docker is typically used to isolate different applications**, such as front-end, back-end, and databases.

In my view, it's not about which will replace the other; both can coexist harmoniously.

![](https://oss.javaguide.cn/javaguide/056c87751b9dd7b56f4264240fe96d00.png)

## Introduction to Docker

### What is Docker?

To be frank, explaining what Docker is, can be challenging. Below, I will clarify what Docker is through four points.

- **Docker is the world's leading software container platform.**
- **Docker** is developed using Google's **Go language**, based on CGroup functionality and namespaces provided by the **Linux kernel**, and technologies like AUFS-type **UnionFS**, **to encapsulate and isolate processes, belonging to operating system-level virtualization technology.** Since the isolated processes are independent of the host and other isolated processes, they are also referred to as containers.
- Docker can automatically perform repetitive tasks, such as setting up and configuring development environments, freeing developers to focus on what truly matters: building great software.
- Users can easily create and use containers, placing their applications into containers. Containers can also be versioned, copied, shared, and modified just like managing ordinary code.

**Docker Philosophy**:

- **Containerization**: Similar to shipping containers, Docker containers bundle applications and all their dependencies, ensuring that they run the same way in any environment.
- **Standardization**: Modes of transport, storage methods, API interfaces.
- **Isolation**: Every Docker container runs in its isolated environment, disconnected from the host and other containers.

### Characteristics of Docker Containers

- **Lightweight**: Multiple Docker containers running on one machine can share the machine's operating system kernel; they can start quickly while consuming minimal computational and memory resources. Images are constructed through file system layers, sharing some common files, which reduces disk usage and allows for faster image downloads.
- **Standardized**: Docker containers are based on open standards, enabling them to run on all mainstream Linux distributions, Microsoft Windows, and any infrastructure including VMs, bare metal servers, and cloud.
- **Secure**: The isolation Docker provides to applications is not limited to isolation from each other but is also independent of the underlying infrastructure. Docker offers strong isolation by default, meaning if an application has issues, it only affects the individual container rather than the entire machine.

### Why Use Docker?

- Docker images provide a complete runtime environment excluding the kernel, ensuring consistent application runtime environments, thus eliminating issues like "this code works fine on my machine"; — consistent runtime environments.
- It can achieve startup times in seconds or even milliseconds, greatly saving time during development, testing, and deployment. — faster startup times.
- Avoiding shared servers, as resources can easily be influenced by other users. — isolation.
- It is adept at managing concentrated server usage pressures; — elastic scaling and quick expansion.
- Applications that run on one platform can be easily migrated to another platform without worrying about changes in the runtime environment leading to application failures. — easy migration.
- By using Docker, continuous integration, continuous delivery, and deployment can be achieved through customized application images. — continuous delivery and deployment.

______________________________________________________________________

## Basic Concepts of Docker

There are three very important basic concepts in Docker: Image, Container, and Repository.

Understanding these three concepts gives you a grasp of the entire lifecycle of Docker.

![](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-build-run.jpeg)

### Image: A Special Filesystem

**The operating system is divided into the kernel and user space**. For Linux, after the kernel starts, it mounts the root filesystem to provide user space support. A Docker image is essentially a root filesystem.

**A Docker image is a special filesystem that, in addition to providing the programs, libraries, resources, and configuration files necessary for container runtime, also includes some configuration parameters prepared for runtime (such as anonymous volumes, environment variables, users, etc.).** Images do not contain any dynamic data and their content does not change after being built.

When Docker was designed, it fully utilized **Union FS** technology and was designed as a **layered storage architecture**. An image is composed of multiple layers of filesystems combined.

**When building images, layers are built one on top of the other, where the previous layer serves as the foundation for the next layer. Each layer will not change after being built, and any changes on the next layer occur only in that layer.** For example, the operation of deleting files from the previous layer does not actually delete the files, but marks them as deleted in the current layer. While the final running container won’t see this file, it follows the image all the while. Therefore, when building images, one needs to be cautious that each layer should preferably only include what's necessary for that layer, and any additional items should be cleaned up before the layer construction ends.

The feature of layered storage also makes image reuse and customization easier. One can even use a previously built image as a base layer and then further add new layers to customize the desired content to build new images.

### Container: The Entity of Image During Runtime

The relationship between an image and a container is analogous to a class and its instances in object-oriented programming; the image is a static definition, while **the container is the entity of the image during runtime. Containers can be created, started, stopped, deleted, and paused, etc.**

**The essence of a container is a process, but unlike processes running directly on the host, container processes run in their independent namespace. As mentioned before, images use layered storage, so do containers.**

**The lifecycle of a container’s storage layer corresponds to that of the container itself; when the container dies, so does its storage layer. Therefore, any information saved in the container's storage layer will be lost when the container is deleted.**

According to Docker's best practices, **containers should not write any data to their storage layer**; the container storage layer should remain stateless. **All file write operations should use data volumes or bind mounts to the host directory**, with read and write occurring directly on the host (or network storage), leading to higher performance and stability. The storage lifespan of data volumes is independent of containers; when the container is deleted, the data volume will not be deleted. Therefore, **with data volumes, containers can be deleted and re-run without losing data.**

### Repository: A Central Place to Store Image Files

Once an image is built, it can be easily run on the current host. However, **if we need to use this image on other servers, we need a centralized storage and distribution service for images, which is what Docker Registry provides.**

A Docker Registry can contain multiple repositories; each repository can hold multiple tags; and each tag corresponds to an image. Therefore, **an image repository is a centralized place for storing image files, similar to code repositories we commonly use.**

Typically, **a repository will contain images of different versions of the same software**, and **tags are commonly used to match different versions of that software**. We can specify the specific version of an image for software using the format `<repository-name>:<tag>`. If no tag is provided, `latest` is assumed as the default tag.

**Here, I’d like to add concepts about public Docker Registry services and private Docker Registries:**

**Public Docker Registry services** are open for users to manage images. Generally, these public services allow users to upload and download public images for free and may offer paid services for managing private images.

The most commonly used public registry service is the official **Docker Hub**, which is also the default registry and boasts a large number of high-quality official images, accessible at: [https://hub.docker.com/](https://hub.docker.com/ "https://hub.docker.com/"). The official description of Docker Hub is:

> Docker Hub is an official service provided by Docker for finding and sharing container images with your team.

For example, if we want to search for the image we need:

![Searching Images with Docker Hub](https://oss.javaguide.cn/github/javaguide/tools/docker/Screen%20Shot%202019-11-04%20at%208.21.39%20PM.png)

In the search results on Docker Hub, there are several key pieces of information to help us choose the appropriate image:

- **OFFICIAL Image**: Indicates that the image is provided and maintained by Docker officially, suggesting higher stability and security.
- **Stars**: Similar to likes, akin to GitHub Stars.
- **Downloads**: Represents how many times the image has been pulled, usually indicating the frequency of image usage.

Of course, aside from directly searching for images on the Docker Hub website, we can also use the `docker search` command to search for images in Docker Hub, yielding consistent results.

```bash
➜  ~ docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   8763                [OK]
mariadb                           MariaDB is a community-developed fork of MyS…   3073                [OK]
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   650                                     [OK]
```

Accessing **Docker Hub** in China may be slow, but domestic cloud service providers offer similar public services like [TenX Cloud Image Library](https://www.tenxcloud.com/ "TenX Cloud Image Library"), [NetEase Cloud Image Service](https://www.163yun.com/product/repo "NetEase Cloud Image Service"), [DaoCloud Image Market](https://www.daocloud.io/ "DaoCloud Image Market"), and [Aliyun Image Library](https://www.aliyun.com/product/containerservice?utm_content=se_1292836 "Aliyun Image Library").

In addition to public services, users can also **set up a private Docker Registry locally**. Docker officially provides a Docker Registry image that can be used directly as a private registry service. The open-source Docker Registry image only provides the server-side implementation of the Docker Registry API, sufficient to support Docker commands, and does not affect usability. However, it lacks graphical interfaces and advanced features like image maintenance, user management, and access control.

### The Relationship Between Image, Container, and Repository

The following image vividly illustrates the relationships among Image, Container, Repository, and Registry/Hub:

![Docker Architecture](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-regitstry.png)

- A Dockerfile is a text file containing a series of instructions and parameters that define how to build a Docker image. When running the `docker build` command and specifying a Dockerfile, Docker reads the instructions from the Dockerfile and incrementally builds a new image, saving it locally.
- The `docker pull` command downloads an image from a specified Registry/Hub to the local environment, defaulting to Docker Hub.
- The `docker run` command creates a new container from a local image and starts it. If the image does not exist locally, Docker tries to pull the image from the Registry/Hub first.
- The `docker push` command uploads the local Docker image to the specified Registry/Hub.

The basic commands mentioned above will be elaborated on later.

### Build, Ship, and Run

Having covered the basic concept of Docker, let's talk about: Build, Ship, and Run.

If you search the Docker official website, you will find the phrase: **"Docker - Build, Ship, and Run Any App, Anywhere."** So what is Build, Ship, and Run all about?

![](https://oss.javaguide.cn/javaguide/tools/docker/docker-build-ship-run.jpg)

- **Build (Building the Image)**: An image is like a shipping container that includes files and running environments and other resources.
- **Ship (Shipping the Image)**: Transporting between the host and the repository; the repository serves like a super dock.
- **Run (Running the Image)**: A running image becomes a container, and the container is where programs run.

The Docker process involves pulling the image from the repository to the local machine and then using a command to run the image, turning it into a container. Thus, we often refer to Docker as dock workers or dock loaders, similar to the Chinese translation of "搬运工人" (搬运 means transporting or moving).

## Common Docker Commands

### Basic Commands

```bash
docker version # Check Docker version
docker images # List all downloaded images, equivalent to: docker image ls command
docker container ls # List all containers
docker ps # List running containers
docker image prune # Clean up temporary and unused image files. -a, --all: Remove all unused images, not just temporary files;
```

### Pulling Images

The `docker pull` command default registry/Hub is Docker Hub. When you execute the `docker pull` command without specifying a registry/Hub address, Docker will pull images from Docker Hub.

```bash
docker search mysql # View mysql-related images
docker pull mysql:5.7 # Pull mysql image
docker image ls # List all downloaded images
```

### Building Images

When running the `docker build` command and specifying a Dockerfile, Docker reads the instructions in the Dockerfile and gradually builds a new image, saving it locally.

```bash
#
# imageName is the name of the image and 1.0.0 is the version number or tag
docker build -t imageName:1.0.0 .
```

Note: The Dockerfile does not necessarily have to be named Dockerfile, nor does it need to be placed in the root directory of the build context. By using the `-f` or `--file` option, any file at any location can be designated as the Dockerfile. However, it is common practice to use the default filename `Dockerfile`, and to place it in the context directory for building images.

### Deleting Images

For example, if we want to delete the downloaded mysql image.

Before deleting an image via `docker rmi [image]` (equivalent to `docker image rm [image]`), we must ensure that this image is not referenced by any container (can be deleted via tag name or image ID). We can view this by using the `docker ps` command we discussed earlier.

```shell
➜  ~ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
c4cd691d9f80        mysql:5.7           "docker-entrypoint.s…"   7 weeks ago         Up 12 days          0.0.0.0:3306->3306/tcp, 33060/tcp   mysql
```

We can see that mysql is referred to by the container with ID c4cd691d9f80; we need to first pause this container using `docker stop c4cd691d9f80` or `docker stop mysql`.

Next, check the id of the mysql image

```shell
➜  ~ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
mysql                   5.7                 f6509bac4980        3 months ago        373MB
```

We can delete it using either the IMAGE ID or the REPOSITORY name.

```shell
docker rmi f6509bac4980 #  or docker rmi mysql
```

### Pushing Images

The `docker push` command is used to upload local Docker images to the specified Registry/Hub.

```bash
# Push the image to a private image repository Harbor
# harbor.example.com is the address of the private image repository, ubuntu is the image name, 18.04 is the version tag
docker push harbor.example.com/ubuntu:18.04
```

Before pushing the image, ensure that the Docker image to be pushed has already been built locally. Also, be sure to log in to the corresponding image repository first.

## Docker Data Management

There are two main ways to manage data within containers:

1. Data Volumes
1. Mounting Host Directories (Bind mounts)

![Docker Data Management](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-data-management.png)

Data volumes are Docker-managed data storage areas with the following characteristics:

- They can be shared and reused between containers.
- Even if a container is deleted, data in the volume will not be automatically deleted, ensuring data persistence.
- Modifications to the volume take effect immediately.
- Updates to the volume do not affect the image.

```bash
# Create a data volume
docker volume create my-vol
# List all data volumes
docker volume ls
# View detailed information of the data volume
docker inspect web
# Delete a specified data volume
docker volume rm my-vol
```

When using the `docker run` command, use the `--mount` flag to mount one or more data volumes into a container.

Additionally, the `--mount` flag allows mounting files or directories from the host directly into the container, enabling the container to access the host's file system directly. The default permission for Docker-mounted host directories is read-write, and users can specify `readonly` for read-only permission.

## Docker Compose

### What is Docker Compose? What is it used for?

Docker Compose is one of Docker's official orchestration projects, written in Python, responsible for quick orchestration of Docker container clusters. With Docker Compose, developers can use a YAML file to configure all the services of an application, allowing all services to be created and started with a single command.

Docker Compose is an open-source project available at: <https://github.com/docker/compose>.

Core Functions of Docker Compose:

- **Multi-container Management**: Allows users to define and manage multiple containers in a single YAML file.
- **Service Orchestration**: Configures the networking and dependencies between containers.
- **One-click Deployment**: With simple commands, such as `docker-compose up` and `docker-compose down`, starting and stopping the entire application becomes straightforward.

Docker Compose simplifies the development, testing, and deployment processes of multi-container applications, improving the productivity of development teams while reducing deployment complexity and management costs.

### Basic Structure of Docker Compose File

The Docker Compose file is the core of the Docker Compose tool, used to define and configure multi-container Docker applications. This file is commonly named `docker-compose.yml` and is written in YAML (YAML Ain’t Markup Language) format.

The basic structure of a Docker Compose file is as follows:

- **Version:** Specifies the version of the Compose file format. The version determines the available configuration options.
- **Services:** Defines each container (service) in the application. Each service can utilize different images, environment settings, and dependencies.
  - **Image:** The container starts from a specified image, which can be a storage repository, tag, or image ID.
  - **Command:** Optional; overrides the default command executed after the container starts. Used for running specific commands or scripts when starting services, commonly used to start applications or execute initialization scripts.
  - **Ports:** Optional; maps the ports of the container to those of the host.
  - **Depends_on:** Dependency configuration option, indicating that if a service depends on other services, those should be started first before the dependent service starts.
  - **Environment:** Optional; sets environment variables required for the service to run.
  - **Restart:** Optional; controls the restart policy of containers. Automatically restarts containers based on the specified strategy when they exit.
  - **Volumes:** Optional; defines the volumes that the service uses for data persistence or sharing data between containers.
  - **Build:** Specifies the context path of the dockerfile for building the image, or detailed configuration properties.
- **Networks:** Defines the network connections between containers.
- **Volumes:** Defines data volumes used for persistence and sharing, commonly used for databases, configuration files, logs, etc.

```yaml
version: "3.8" # Define the version, indicating the version of docker-compose syntax in use
services: # Services can contain multiple entries
    servicename1: # Service name; can be used as a DNS name in the internal bridge network, if not in swarm mode, it's similar to a name specified in docker run. 
    # In swarm mode, it is a logical abstraction of multiple containers.
        image: # Name of the image
        command: # Optional; if set, it overrides the default CMD command in the image
        environment: # Optional; equivalent to the --env option in docker container run to set environment variables
        volumes: # Optional; equivalent to the -v option in docker container run for binding data volumes
        networks: # Optional; equivalent to the --network option in docker container run to specify networks
        ports: # Optional; equivalent to the -p option in docker container run to specify port mapping
        restart: # Optional; controls the restart policy of containers
        build: # Context for building
        depends_on: # Service dependency configuration
    servicename2:
        image:
        command:
        networks:
        ports:
    servicename3:
    #...
volumes: # Optional, data volumes that need to be created; similar to docker volume create
  db_data:
networks: # Optional; equivalent to docker network create
```

### Common Docker Compose Commands

#### Starting

`docker-compose up` will create and start containers based on the services defined in the `docker-compose.yml` file, connecting them to the default network.

```bash
# Look for the docker-compose.yml file in the current directory and start the application based on defined services
docker-compose up
# Start in the background
docker-compose up -d
# Force recreate all containers even if they already exist
docker-compose up --force-recreate
# Rebuild images
docker-compose up --build
# Specify the name of the service to start rather than starting all services
# Multiple services can be specified by separating with spaces.
docker-compose up service_name
```

Moreover, if the Compose file name is not `docker-compose.yml`, it can also be specified using the `-f` option.

```bash
docker-compose -f docker-compose.prod.yml up
```

#### Stopping

`docker-compose down` is used to stop and remove all containers and networks started by `docker-compose up`.

```bash
# Look for the docker-compose.yml file in the current directory
# Remove all started containers, networks, and volumes based on the definition in the file.
docker-compose down
# Stop containers but do not remove them
docker-compose down --stop
# Specify particular services to stop and remove instead of all services
# Multiple services can be specified by separating with spaces.
docker-compose down service_name
```

If the Compose file name is not `docker-compose.yml`, it can also be specified using the `-f` option.

```bash
docker-compose -f docker-compose.prod.yml down
```

#### Viewing

`docker-compose ps` is used to check the status information of all the containers started with `docker-compose up`.

```bash
# View status information of all containers
docker-compose ps
# Show only the service names
docker-compose ps --services
# View containers of a specific service
docker-compose ps service_name
```

#### Others

| Command                  | Description                        |
| ------------------------ | ---------------------------------- |
| `docker-compose version` | View version                       |
| `docker-compose images`  | List all images used by containers |
| `docker-compose kill`    | Force stop service containers      |
| `docker-compose exec`    | Execute commands in a container    |
| `docker-compose logs`    | View logs                          |
| `docker-compose pause`   | Pause services                     |
| `docker-compose unpause` | Resume services                    |
| `docker-compose push`    | Push service images                |
| `docker-compose start`   | Start currently stopped containers |
| `docker-compose stop`    | Stop currently running containers  |
| `docker-compose rm`      | Delete stopped service containers  |
| `docker-compose top`     | View processes                     |

## Underlying Principles of Docker

First of all, Docker is software based on lightweight virtualization technology. So what is virtualization technology?

In simple terms, virtualization technology can be defined as:

> Virtualization technology is a resource management technique that abstracts various [physical resources](https://zh.wikipedia.org/wiki/%E8%A8%88%E7%AE%97%E6%A9%9F%E7%A7%91%E5%AD%B8 "Physical Resources") of computers (such as [CPU](https://zh.wikipedia.org/wiki/CPU "CPU"), [memory](https://zh.wikipedia.org/wiki/%E5%86%85%E5%AD%98 "Memory"), [disk space](https://zh.wikipedia.org/wiki/%E7%A3%81%E7%9B%98%E7%A9%BA%E9%97%B4 "Disk Space"), [network adapters](https://zh.wikipedia.org/wiki/%E7%B6%B2%E8%B7%AF%E9%81%A9%E9%85%8D%E5%99%A8 "Network Adapters"), etc.), transforming them into a form that can be divided and combined into one or more computer configurations. This breaks the indivisibility barrier among physical structures, enabling users to apply these computer hardware resources better than originally configured. The new virtual components are not restricted by the existing resource setup, location, or physical arrangement. Typically, the resources referred to under virtualization include computational capabilities and data storage.

Docker technology is based on LXC (Linux Containers) virtualization technology.

> LXC, short for Linux Containers, is an operating system-level virtualization technology providing a user-space interface for Linux kernel container functionalities. It packages application software systems into a software container containing the application code and necessary operating system kernel and libraries. It allocates usable hardware resources to different software containers through a unified namespace and shared API, creating independent sandbox environments for application execution, allowing Linux users to easily create and manage system or application containers.

The LXC technology mainly utilizes the CGroup functionality and namespaces provided by the Linux kernel to give software an independent operating system runtime environment.

**Introduction to cgroup and namespace:**

- **Namespace** is a way that the Linux kernel uses to isolate kernel resources. By using namespaces, processes can only see a portion of resources related to them while other processes do not even realize the existence of each other. The specific implementation involves assigning one or more processes' related resources to the same namespace. Linux namespaces encapsulate and isolate global system resources, ensuring that processes within different namespaces hold independent global system resources; changes to the resource within one namespace only affect the processes in that namespace and have no impact on those in other namespaces.

  (The above content regarding namespace introduction is sourced from <https://www.cnblogs.com/sparkdev/p/9365405.html>, and more on namespaces can be found in that article.)

- **CGroup** is short for Control Groups, a mechanism provided by the Linux kernel that can limit, record, and isolate the physical resources used by process groups (like CPU memory I/O, etc.).

  (The above content regarding CGroup introduction is sourced from <https://www.ibm.com/developerworks/cn/linux/1506_cgroup/index.html>, and more on CGroup can be found in that article.)

**Comparison of cgroup and namespace:**

Both serve to group processes, but their functions differ fundamentally. Namespaces aim to isolate resources between process groups, while cgroups are used for uniform monitoring and resource limiting of a group of processes.

## Conclusion

This article mainly elaborates on some common concepts and commands in Docker. For a practical guide from scratch, you can refer to [Docker Getting Started](https://javaguide.cn/tools/docker/docker-in-action.html), which provides detailed content!

Additionally, I recommend a high-quality open-source book [“Docker from Beginner to Practice”](https://yeasy.gitbook.io/docker_practice/introduction/why "Docker from Beginner to Practice"), which contains very up-to-date content since the book is open-sourced and can be modified anytime.

![Homepage of “Docker from Beginner to Practice” Website](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-getting-started-practice-website-homepage.png)

## References

- [Docker Compose: A Comprehensive Guide from Scratch to Practical Applications](https://juejin.cn/post/7306756690727747610)
- [Linux Namespace and Cgroup](https://segmentfault.com/a/1190000009732550 "Linux Namespace and Cgroup")
- [LXC vs Docker: Why Docker is Better](https://www.upguard.com/articles/docker-vs-lxc "LXC vs Docker: Why Docker is Better")
- [Introduction, Application Cases, and Principles Description of CGroup](https://www.ibm.com/developerworks/cn/linux/1506_cgroup/index.html "Introduction, Application Cases, and Principles Description of CGroup")

<!-- @include: @article-footer.snippet.md -->
