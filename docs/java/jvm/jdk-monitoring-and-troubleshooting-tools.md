I don't...
Title: Summary of JDK Monitoring and Trouble Management Tools
Category:
Tag:

- JVM.
  I don't...

# JDK Command Line Tool

These commands are in the bin directory under the JDK installation directory:

- **`jps`** (JVM Property Status): UNIX-like `ps` command. For viewing information such as start-up categories, input parameters, and Java virtual machine parameters for all Java processes;
- **`jstat`** (JVM Statistics Monitoring Tool): for the collection of operational data on various aspects of the HotSpot virtual machine;
- **`jinfo`** (Configuration Info for Java): Configuration Info for Java, displaying virtual machine configuration information;
- **`jmap`** (Memory Map for Java): Generate a dump snapshot;
- **`jhat`** (JVM Heap Dump Browser): For analysis of the Heapdump file, it creates an HTTP/HTML server that allows users to view the results in the browser. JDK9 removed `jhat`;
- **`jstack`** (Stack Trace for Java): Generate a linear snapshot of the current moment of the virtual machine, which is the collection of a method stack being implemented at each of the lines in the current virtual machine.

#### `jps`: View All Java Processes

`jps` (JVM Property Status) commands similar to UNIX's `ps` commands.

`jps`: the only IID (Local Virtual Machine Identifier, LVMID) that displays the name of the main type of virtual machine execution and the local virtual machine of these processes. `jps -q`: The only local virtual machine with an output process.

```
Powershell
C:\Users\SnailClimb>jps
7360 NettyClient2
17396
7972 Launcher
16504 Jps
17340 NettyServer
```

`jps -l`: The full name of the output main class if the process is executed by the Jar package, output the Jar path.

```
Powershell
== sync, corrected by elderman == @elder_man
7360 first Netty Demo.
17396
7972 org.jetbrains.jps.cmdline.Launcher
16492 sun.tools.jps.Jps
17340 first Netty Demo.
```

`jps -v`: JVM parameters at the start of the output virtual machine process.

`jps -m`: Parameters for the output to be passed to the Java process Main() function.

### `jstat`: Monitor All Operational Status Information of Virtual Machines

`jstat` (JVM Statistics Monitoring Tool) uses command line tools to monitor all operational information on virtual machines. It displays local or remote (requires remote host RMI support) type information, memory, garbage collection, JIT compilation, etc. operating data, which, in the absence of GUI, is provided only on a server with a pure text console environment, which will be the preferred tool for locating virtual performance during the operation.

**`jstat` command format:**

```
Powershell
jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]
```

For example, `jstat -gc -h3 31736 1000` for gc id 31736 id printing records every 1000 ms, printing stop 10 times, printing the head of the indicator after every 3 lines.

**Common options are as follows:**

- `jstat -class <vmid>`: display relevant information for ClassLoader;
- `jstat -compiler <vmid>`: display relevant information compiled by JIT;
- `jstat -gc <vmid>`: displaying stacks of information relevant to GC;
- `jstat -gccapacity <vmid>`: showing the capacity and use of generations;
- `jstat -gcnew <vmid>`: displaying new generation information;
- `jstat -gcnewcapacity <vmid>`: showing the size and use of the new generation;
- `jstat -gcold <vmid>`: showing behavior statistics of old and permanent generations, starting with JDK1.8, which represents only old age, since permanent generations are removed;
- `jstat -goldcapacity <vmid>`: showing the size of the old age;
- `jstat -gcpermcapacity <vmid>`: display of permanent generational size, starting with JDK1.8, the option does not exist because the permanent generational is removed;
- `jstat -gcutil <vmid>`: display of garbage collection information;

In addition, the `-t` parameter can add a Timestamp column to the output information to show the running time of the program.

### `jinfo`: Real-Time View and Adjust Virtual Machine Parameters

`jinfo <vmid>`: Output all parameters and system properties of the current JVM process (part I is system properties and part II is JVM
