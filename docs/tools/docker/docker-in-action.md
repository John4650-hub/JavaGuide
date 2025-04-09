I don't...
Title: Docker Field
Category: Development Tools
Tag:
Docker.
I don't...

# Docker Introduction

Before proceeding, a brief introduction to Docker; more Docker concept presentations can be found in the previous article [Docker Core Concept Summary](./Docker-intro.md).

# What's Docker?

To be honest, it's not easy to say what Docker is, and I'm going to tell you in four points what it is.

- Docker is the world's leading software container platform, developed in **Go language**.
- Docker can automatically carry out repetitive tasks, such as setting up and configuring the development environment, thus freeing developers.
- Users can easily create and use containers and place their applications in them. Packages can also be used for version management, copying, sharing, and modification, as is the case for managing common code.
- Docker can **cover the process, which is a virtualization technology at the operating system level.** Also referred to as a container because the segregation process is independent of the host and other segregation processes.

Official address: <https://www.docker.com/>.

![Knower Packages](https://oss.javaguide.cn/github/javaguide/tools/docker/container.png)

Why do you use Docker?

Docker allows developers to package their applications and rely on them for a lightweight, portable container, and then deploy them on any popular Linux machine or virtualization.

Packages are full-scale sandbox mechanisms, with no interfaces between them (iPhone-like apps) and, more importantly, they are extremely low-cost.

In the traditional development process, our projects usually require the use of MySQL, Redis, FastDFS, etc., environments that require us to download and configure manually, the installation of which is extremely complex, and the operation of different systems is different.

The presence of Docker has successfully solved this problem by installing software environments such as MySQL and Redis in containers, separating applications and environmental structures, with the advantage of:

1. Consistent operating environment, which allows easier migration.
2. Covering the process so that there is no mutual influence between the packages and more efficient use of system resources.
3. Replicating multiple and consistent containers by mirroring.

Moreover, the reasons for using Docker have already been given in this open-source book.

(https://oss.javaguide.cn/github/javaguide/tools/docker/201041220015698.png)

# Docker Installation

# Windows

Docker is then installed, using Windows as an example, to access Docker's network:

(https://oss.javaguide.cn/github/javaguide/tools/docker/docker-install-windows.png)

And then click `Get Started`:

(https://oss.javaguide.cn/github/javaguide/tools/docker/docker-install-windows-download.png)

Click here to download `Download for Windows`.

If your computer is running `Windows 10, 64-bit professional version`, you will need to turn on `Hyper-V` before you install Docker, as follows. Open the control panel, select programs:

[Opens Hyper-V](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-windows-hyperv.png)

Click `Enable or disable Windows features`:

[Opens Hyper-V](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-windows-hyperv-enable.png)

Check `Hyper-V`, click OK:

[Opens Hyper-V](https://oss.javaguide.cn/github/javaguide/tools/docker/docker-windows-hyperv-check.png)

Once the change is completed, the computer needs to be restarted.

With `Hyper-V` on, we can install Docker, open the installation, and wait for a moment to click on `OK`:

(https://oss.javaguide.cn/github/javaguide/tools/docker/docker-windows-hyperv-install.png)

After the installation has been completed, we still need to restart the computer, and once it has been restarted, if you see the following:

(https://oss.javaguide.cn/github/javaguide/tools/docker/docker-windows-hyperv-wsl2.png)

It means asking if we use WSL2, a Linux subsystem based on Windows, and if we cancel here, it will use the `Hyper-V` virtual machine that we have previously selected.

The specific use of Docker Desktop is not presented here because it is a graphical operation.

# Mac

Just use Homebrew for installation.

```shell
# I'm going to take a look at this.
```

# Linux

Here's how to install Docker in Linux, for example in CentOS7.

In a test or development environment, Docker official, in an effort to simplify the installation process, has provided an easy set of installation scripts, which, when implemented, automatically prepares all the preparatory work and installs a stable Docker version in the system.

```shell
# You're not gonna get any more of this.
```

```shell
# I don't know, she gets-docker.
```

Directly start the service after installation:

```shell
# You know, systemctl start docker.
``