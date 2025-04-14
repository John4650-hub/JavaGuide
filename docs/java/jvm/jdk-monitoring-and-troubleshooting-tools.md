---
title: Summary of JDK Monitoring and Fault Handling Tools
category: Java
tag:
  - JVM
---

## JDK Command Line Tools

These commands are located in the bin directory of the JDK installation:

- **`jps`** (JVM Process Status): Similar to the UNIX `ps` command. Used to view information about all Java processes, including the main class, input parameters, and JVM parameters;
- **`jstat`** (JVM Statistics Monitoring Tool): Used to collect various runtime data from the HotSpot virtual machine;
- **`jinfo`** (Configuration Info for Java): Displays configuration information for the virtual machine;
- **`jmap`** (Memory Map for Java): Generates heap dump snapshots;
- **`jhat`** (JVM Heap Dump Browser): Used to analyze heap dump files. It sets up an HTTP/HTML server for users to view analysis results in a browser. `jhat` was removed in JDK9;
- **`jstack`** (Stack Trace for Java): Generates a snapshot of the threads in the virtual machine at the current moment, which is a collection of the method stacks being executed by each thread in the virtual machine.

### `jps`: View All Java Processes

The `jps` (JVM Process Status) command is similar to the UNIX `ps` command.

`jps`: Displays the main class name executed by the virtual machine and the local virtual machine unique ID (Local Virtual Machine Identifier, LVMID). `jps -q`: Outputs only the local virtual machine unique ID of the processes.

```powershell
C:\Users\SnailClimb>jps
7360 NettyClient2
17396
7972 Launcher
16504 Jps
17340 NettyServer
```

`jps -l`: Outputs the full name of the main class. If the process is executing a Jar file, it outputs the Jar path.

```powershell
C:\Users\SnailClimb>jps -l
7360 firstNettyDemo.NettyClient2
17396
7972 org.jetbrains.jps.cmdline.Launcher
16492 sun.tools.jps.Jps
17340 firstNettyDemo.NettyServer
```

`jps -v`: Outputs the JVM parameters used when starting the virtual machine process.

`jps -m`: Outputs the parameters passed to the Java process's main() function.

### `jstat`: Monitor Various Runtime Status Information of the Virtual Machine

`jstat` (JVM Statistics Monitoring Tool) is a command-line tool used to monitor various runtime status information of the virtual machine. It can display class information, memory, garbage collection, JIT compilation, and other runtime data from local or remote (requires RMI support from the remote host) virtual machine processes. It is the preferred tool for locating virtual machine performance issues on servers without a GUI, providing only a pure text console environment.

**`jstat` command usage format:**

```powershell
jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]
```

For example, `jstat -gc -h3 31736 1000 10` means analyzing the GC situation of the process with ID 31736, printing records every 1000ms, stopping after printing 10 times, and printing the header after every 3 lines.

**Common options are as follows:**

- `jstat -class vmid`: Displays information related to ClassLoader;
- `jstat -compiler vmid`: Displays information related to JIT compilation;
- `jstat -gc vmid`: Displays heap information related to GC;
- `jstat -gccapacity vmid`: Displays the capacity and usage of each generation;
- `jstat -gcnew vmid`: Displays information about the young generation;
- `jstat -gcnewcapacity vmid`: Displays the size and usage of the young generation;
- `jstat -gcold vmid`: Displays behavior statistics for the old generation and permanent generation. Starting from JDK 1.8, this option only represents the old generation, as the permanent generation has been removed;
- `jstat -gcoldcapacity vmid`: Displays the size of the old generation;
- `jstat -gcpermcapacity vmid`: Displays the size of the permanent generation. Starting from JDK 1.8, this option no longer exists, as the permanent generation has been removed;
- `jstat -gcutil vmid`: Displays garbage collection information;

Additionally, adding the `-t` parameter can add a Timestamp column to the output information, showing the program's runtime.

### `jinfo`: View and Adjust Virtual Machine Parameters in Real Time

`jinfo vmid`: Outputs all parameters and system properties of the current JVM process (the first part is system properties, and the second part is JVM parameters).

\`
