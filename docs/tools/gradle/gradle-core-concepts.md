---
title: Summary of Core Concepts of Gradle
category: Development Tools
head:
  -   - meta
      - name: keywords
        content: Gradle, Groovy, Gradle Wrapper, Gradle packaging, Gradle plugins
  -   - meta
      - name: description
        content: Gradle is an automation project build tool that runs on JVM, designed to help us automate project builds.
---

> This content is primarily organized based on the official Gradle documentation, with corresponding reductions made to retain the most important parts. It does not involve practical applications, and is mainly focused on introducing key concepts.

The content on Gradle is optional, and you can decide whether to learn it based on your own needs. Currently, Maven is still more commonly used domestically.

## Introduction to Gradle

The official Gradle documentation describes Gradle as follows:

> Gradle is an open-source [build automation](https://en.wikipedia.org/wiki/Build_automation) tool flexible enough to build almost any type of software. Gradle makes few assumptions about what you’re trying to build or how to build it. This makes Gradle particularly flexible.
>
> Gradle is an open-source build automation tool that is flexible enough to build almost any type of software. Gradle makes few assumptions about what you want to build or how to build it, making it especially flexible.

In simple terms, Gradle is an automation project build tool that runs on JVM, designed to help us automate project builds.

For developers, the main purposes of Gradle are threefold:

1. **Project Building**: Provides a standard, cross-platform automated project building method.
1. **Dependency Management**: Conveniently and quickly manages project dependencies (JAR packages), avoiding version conflicts between resources.
1. **Unified Development Structure**: Provides a standard and unified project structure.

Gradle build scripts are written in Groovy or Kotlin, offering strong expressiveness and flexibility.

## Introduction to Groovy

Gradle is a program that runs on JVM, and it can use Groovy to write build scripts.

Groovy is a scripting language that runs on JVM, which is a dynamically typed language based on Java extensions. Its syntax is very similar to Java and it can use Java libraries. Groovy can be used for object-oriented programming as well as for pure scripting. In its design, it incorporates the excellent features of Java, Python, Ruby, and Smalltalk languages, such as dynamic type conversion, closures, and metaprogramming support.

We can learn Groovy in a way similar to learning Java, making the learning cost relatively low. Even if you forget Groovy syntax during development, you can continue coding using Java syntax.

There are many JVM-based languages such as Groovy, Kotlin, Java, and Scala, all of which will eventually compile to Java bytecode and run on JVM.

## Advantages of Gradle

Gradle is a new-generation build system that offers many advantages like efficiency and flexibility, widely used in Java development. Not only does Android adopt it as the official build system, but more and more Java projects like Spring Boot are also gradually migrating to Gradle.

- In terms of flexibility, Gradle supports writing scripts in Groovy, emphasizing the flexibility of the build process, making it suitable for projects with high complexity, capable of handling very complex builds.
- In terms of granularity, Gradle's builds are refined down to each task. Furthermore, all Task source codes are open-source, and once we master the entire packaging process, we can dynamically change the execution flow by modifying its Task.
- In terms of extensibility, Gradle supports a plugin mechanism, allowing us to reuse these plugins as simply and conveniently as reusing libraries.

## Introduction to Gradle Wrapper

The official Gradle documentation describes Gradle Wrapper as follows:

> The recommended way to execute any Gradle build is with the help of the Gradle Wrapper (in short just “Wrapper”). The Wrapper is a script that invokes a declared version of Gradle, downloading it beforehand if necessary. As a result, developers can get up and running with a Gradle project quickly without having to follow manual installation processes saving your company time and money.
>
> The recommended method for executing Gradle builds is to use Gradle Wrapper (simply “Wrapper”). The Wrapper is a script that calls a specified Gradle version, downloading it beforehand if necessary. Thus, developers can quickly start and run Gradle projects without having to follow manual installation processes, saving time and money for the company.

We can call Gradle Wrapper the Gradle packaging tool, which re-packages Gradle, allowing all Gradle build methods to run with the help of the Gradle packaging tool.

The workflow diagram of Gradle Wrapper is as follows (image source: [Gradle Wrapper official documentation](https://docs.gradle.org/current/userguide/gradle_wrapper.html)):

![Wrapper Workflow](https://oss.javaguide.cn/github/javaguide/csdn/efa7a0006b04051e2b84cd116c6ccdfc.png)

The entire process mainly consists of the following three steps:

1. Initially, when we create it, if the specified version has not been downloaded, it will first download the corresponding version zip package from Gradle's server;
1. After the download is complete, it needs to be unzipped and the batch file executed;
1. Subsequent builds of the project will reuse this unzipped version of Gradle.

Gradle Wrapper provides us with the following benefits:

1. Standardizes the project on the specified Gradle version, resulting in more reliable and robust builds.
1. Allows running Gradle projects without installing the Gradle environment on our computers.
1. Providing new Gradle versions for different users and execution environments (such as IDEs or continuous integration servers) is as simple as changing the Wrapper definition.

### Generating Gradle Wrapper

To generate the Gradle Wrapper, you need to configure the Gradle environment variables locally. Gradle has a built-in Wrapper Task, and executing the `gradle wrapper` command in the project's root directory will help us generate the Gradle Wrapper.

When executing the `gradle wrapper` command, you can specify some parameters to control the generation of the wrapper. There are two specific configuration parameters as follows:

- `--gradle-version` is used to specify the version of Gradle to be used.
- `--gradle-distribution-url` is used to specify the URL for downloading the Gradle version. The pattern for this value is `http://services.gradle.org/distributions/gradle-${gradleVersion}-bin.zip`.

After executing the `gradle wrapper` command, the Gradle Wrapper will be generated, creating the following files in the project root directory:

```plain
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
└── gradlew.bat
```

The meaning of each file is as follows:

- `gradle-wrapper.jar`: Contains the runtime logic code of Gradle.
- `gradle-wrapper.properties`: Defines the Gradle version number and runtime behavior attributes of Gradle.
- `gradlew`: The wrapper script for executing Gradle commands on Linux platforms.
- `gradlew.bat`: The wrapper script for executing Gradle commands on Windows platforms.

The content of the `gradle-wrapper.properties` file is as follows:

```properties
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-6.0.1-bin.zip
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
```

- `distributionBase`: The parent directory where Gradle is stored after unpacking.
- `distributionPath`: The subdirectory of the directory specified by `distributionBase`. `distributionBase+distributionPath` is the concrete directory where Gradle is stored after unpacking.
- `distributionUrl`: The download address for the specified version of the Gradle zip package.
- `zipStoreBase`: The parent directory where the Gradle zip package is stored after downloading.
- `zipStorePath`: The subdirectory of the directory specified by `zipStoreBase`. `zipStoreBase+zipStorePath` is where the Gradle zip package is stored.

### Updating Gradle Wrapper

There are two ways to update the Gradle Wrapper:

1. Change the `distributionUrl` field and then execute the Gradle command.
1. Execute the `gradlew` command: `gradlew wrapper --gradle-version [version]`.

The following command will upgrade the Gradle version to 7.6.

```shell
gradlew wrapper --gradle-version 7.6
```

The `distributionUrl` property in the `gradle-wrapper.properties` file will also change.

```properties
distributionUrl=https\://services.gradle.org/distributions/gradle-7.6-all.zip
```

### Customizing Gradle Wrapper

Gradle has a built-in Wrapper Task, so building the Gradle Wrapper will generate a property file for the Gradle Wrapper that can be set through a custom Wrapper Task. For example, if we want to change the Gradle version to 7.6, we can set it like this:

```javascript
task wrapper(type: Wrapper) {
    gradleVersion = '7.6'
}
```

You can also set the download address for the Gradle distribution zip package and the local storage path after unpacking Gradle, etc.

```groovy
task wrapper(type: Wrapper) {
    gradleVersion = '7.6'
    distributionUrl = '../../gradle-7.6-bin.zip'
    distributionPath=wrapper/dists
}
```

The `distributionUrl` property can be set to a local project directory, or you can set it to a network address.

## Gradle Tasks

In Gradle, a task is a single unit of work executed during the build.

Gradle's build is based on tasks, and when you run a project, you are actually executing a series of tasks such as compiling Java source tasks and generating JAR file tasks.

The declaration of a task is as follows (there are other declaration methods as well):

```groovy
// Declare a task named helloTask
task helloTask {
    doLast {
        println "Hello"
    }
}
```

After creating a task, you can add different actions to the task as needed. The above `doLast` adds an action to the end of the queue.

```groovy
// Add an action at the beginning of the Action queue
Task doFirst(Action<? super Task> action);
Task doFirst(Closure action);

// Add an action at the end of the Action queue
Task doLast(Action<? super Task> action);
Task doLast(Closure action);

// Remove all actions
Task deleteAllActions();
```

A task can have multiple actions, executed in order from the head of the queue to the tail.

An Action represents a function or method; each task is an execution graph composed of a series of actions in sequence.

The keyword for declaring task dependencies is `dependsOn`, which supports specifying one or more dependencies:

```groovy
task first {
    doLast {
        println "+++++first+++++"
    }
}
task second {
    doLast {
        println "+++++second+++++"
    }
}

// Specify multiple task dependencies
task print(dependsOn: [second, first]) {
    doLast {
        logger.quiet "Specifying multiple task dependencies"
    }
}

// Specify a single task dependency
task third(dependsOn: print) {
    doLast {
        println '+++++third+++++'
    }
}
```

Before executing a task, its dependency tasks will be executed first.

We can also set default tasks, which will be executed even if we do not call the default tasks in the script.

```groovy
defaultTasks 'clean', 'run'

task clean {
    doLast {
        println 'Default Cleaning!'
    }
}

task run {
    doLast {
        println 'Default Running!'
    }
}
```

Gradle also has many built-in tasks, such as copy (copy files) and delete (delete files).

```groovy
task deleteFile(type: Delete) {
    delete "C:\\Users\\guide\\Desktop\\test"
}
```

## Gradle Plugins

Gradle provides a core set of build mechanisms, while Gradle plugins represent specific build logic that runs on this mechanism. Essentially, Gradle plugins are similar to `.gradle` files, and you can think of them as tools that encapsulate a series of tasks and execute them.

Gradle plugins can be mainly divided into two categories:

- Script plugins: Simple script files that can be imported into other build scripts.
- Binary plugins/object plugins: Defined in a separate plugin module, with other modules applying plugins through Plugin IDs. Because this method is more friendly for publishing and reusing, the Gradle plugins we generally encounter refer to binary plugins.

Although the essence of Gradle plugins and `.gradle` files is the same, and `.gradle` files can also achieve functionalities similar to Gradle plugins, Gradle plugins feature independent modules for encapsulating build logic, which enhances the overall experience during development and usage.

- **Logic Reuse:** Providing the same logic to multiple similar projects to reduce the overhead of maintaining similar logic. Although `.gradle` files can also achieve logic reuse, the encapsulation of Gradle plugins is better.
- **Component Publishing:** Plugins can be published to Maven repositories for management, allowing other projects to use them via Plugin IDs. Although `.gradle` files can also be referenced from a remote path, plugins offer a cleaner approach.
- **Build Configuration:** Gradle plugins can declare plugin extensions to expose configurable attributes, providing customization capabilities. Although `.gradle` files can achieve this, implementation can be more cumbersome.

## Gradle Build Lifecycle

The lifecycle of a Gradle build consists of three phases: **Initialization Phase, Configuration Phase**, and **Execution Phase**.

![](https://oss.javaguide.cn/github/javaguide/csdn/dadbdf59fccd9a2ebf60a2d018541e52.png)

Custom hooks can be added between the initialization and configuration phases, after the configuration phase, and after the execution phase.

![](https://oss.javaguide.cn/github/javaguide/csdn/5c297ccc4dac83229ff3e19caee9d1d2.png)

### Initialization Phase

Gradle supports single-project and multi-project builds. During the initialization phase, Gradle determines which projects will participate in the build and creates a [Project instance](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html) for each project. Essentially, this executes the `settings.gradle` script, reading how many Project instances are present in the project.

### Configuration Phase

During the configuration phase, Gradle parses the `build.gradle` files of each project, creates a subset of tasks to be executed, and determines the relationships between various tasks, so that they can be executed in order during the execution phase, initializing configurations for the tasks.

Each `build.gradle` corresponds to a Project object, and the code executed during the configuration phase includes various statements, closures, and configuration statements in Tasks from the `build.gradle`.

At the end of the configuration phase, Gradle creates a **Directed Acyclic Graph** based on the task dependencies.

### Execution Phase

During the execution phase, Gradle executes the tasks that were created and configured in the configuration phase.

## References

- Gradle Official Documentation: <https://docs.gradle.org/current/userguide/userguide.html>
- Gradle Beginner's Tutorial: <https://www.imooc.com/wiki/gradlebase>
- Quick Start with Groovy: <https://cloud.tencent.com/developer/article/1358357>
- Detailed Explanation of Gradle Life Cycle: <https://juejin.cn/post/7067719629874921508>
- Hands-On Custom Gradle Plugin – Gradle Series (2): <https://www.cnblogs.com/pengxurui/p/16281537.html>
- Gradle Pitfall Guide – Understanding Plugin, Task, Build Process: <https://juejin.cn/post/6889090530593112077>

<!-- @include: @article-footer.snippet.md -->
