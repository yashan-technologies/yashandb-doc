## collection host

This command is used to perform a basic information check on the server.

|Option |Meaning |
| -------------- | ------------------------------------------------------------ |
| *-c,--cluster*   | The cluster name of YashanDB                             |
| *-F,--format*    | The format of the server check results, options are json and html |
| *-o,--output*    | The output directory for the server check results and reports |
| *-n,--name*      | The identifier name for the server check results and reports |
| *-t,--toml*      | Use configuration items in the toml file; please refer to [Server Check Configuration File](../Configuration Files/Server Check Configuration File) |
| *--host*         | If the cluster parameter is specified, it can be a hostid; if not specified, it defaults to the server configuration file hosts.toml |
| *--disable*      | Suppress task progress output                             |
| *-d,--child*     | Show information on child task execution                 |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
# Execute the server check on the local machine
$ yasboot collection host

# Use the configuration items from the toml file for the check
$ yasboot collection host -t yascheck.toml

# Execute the server check on the local machine and generate a front-end report output to the specified directory
$ yasboot collection host -F html --output /home/yashan/check

# Execute agent server check
$ yasboot collection host -c yashan

# Check the target server of the agent
$ yasboot collection host -c yashandb --host host0001

# Check the agent and generate a front-end report to the target directory with the identifier name as yashan
$ yasboot collection host -c yashandb -t yascheck.toml -n yashan -o /home/yashan/check -F html

# Use the servers in the hosts.toml file to execute SSH for the check
$ yasboot collection host -t yascheck.toml --host hosts.toml
$ yasboot collection host --host hosts.toml

# Use the servers in the hosts.toml file to execute SSH for the check and generate the report's target directory
$ yasboot collection host -t yascheck.toml --host hosts.toml -n yashan -o /home/yashan/check -F html
```

<span id="CheckItems" name="CheckItems"></span>

### Check Items

Server checks are divided into 6 modules: hardware check, service check, system parameter check, resource consumption, network IO test, and disk IO test. 

You can specify whether to enable the check items in the [Server Check Configuration File](../Configuration Files/Server Check Configuration File) and then specify the configuration file when executing the command.

**Hardware Check**

|Check Item |Check Content |
| -------------- | ---------------------------------- |
| CPU                 | Name (Name)                                      |
|                     | Architecture (Arch)                              |
|                     | Physical CPU Cores (PhysicalCPU)                |
|                     | Logical CPU Cores (LogicalCPU)                   |
|                     | Cores per CPU (CoresPerCPU)                      |
|                     | Total Cores (TotalCores)                         |
| Disk (Disk)        | Device Name (Device)                             |
|                     | Filesystem (Filesystem)                          |
|                     | Size (Size)                                      |
|                     | Used (Used)                                      |
|                     | Available (Available)                            |
|                     | Used Percent (UsedPercent)                        |
|                     | Mounted On (MountedOn)                           |
| Network (Net)      | Network Adapters (NetworkAdapters)               |
|                | DNS                                |
|                     | Gateway (Gateway)                                |
|                     | Network Connection Status (NetworkConnection)    |
| Memory (Memory)    | Memory Type (Type)                               |
|                     | Total Memory Space (Total)                       |
|                     | Used (Used)                                      |
|                     | Free Space (Free)                                |
|                     | Shared Memory Space (Shared)                     |
|                     | Cache / buffer (BuffersOrCached)                 |
|                     | Available Space (Available)                       |

**System Parameter Check**

|Check Item |Check Content |
| ---------------------- | ------------------------------- |
| Base Information (BaseInfo)  | Operating System Name (OS)             |
|                         | Operating System Version (Release)        |
|                         | Operating System Kernel (Kernel)          |
|                         | Server Name (Hostname)                    |
|                         | Security Enhanced Service (SELinux)      |
|                         | IO Scheduling Policy (IOScheduleModel)   |
|                         | Language and Encoding (LanguageAndEncode) |
|                         | Current Time (TimeNow)                   |
|                         | Last Boot Time (LastBootTime)            |
|                         | Uptime (UpTime)                          |
| System Parameters (Sysctl)    | Dirty Background Ratio (DirtyBackgroundRatio)  |
|                        | DirtyExpireCentisecs            |
|                        | DirtyRatio                      |
|                        | MaxMapCount                     |
|                        | MinFreeKbytes                   |
|                        | OvercommitMemory                |
|                        | OvercommitRatio                 |
|                        | Swappiness                      |
|                        | VfsCachePressure                |
|                        | IPV4LocalPortRange              |
| Resource Limit Parameters (Ulimit) | Core File Size (CoreFileSize)        |
|                        | DatasSegSize                    |
|                        | SchedulingPriority              |
|                        | FileSize                        |
|                        | PendingSignals                  |
|                        | MaxLockedMemory                 |
|                        | MaxMemorySize                   |
|                        | OpenFiles                       |
|                        | PipeSize                        |
|                        | POSIXMessageQueues              |
|                        | RealTimePriotity                |
|                        | StackSize                       |
|                        | CpuTime                         |
|                        | MaxUserProcesses                |
|                        | VirtualMemory                   |
|                        | FileLocks                       |
| User Check (UserInfo)  | Privileged Users (PrivilegeUsers)         |
|                         | Sudo Privileged Users (Sudoers)           |
|                         | Users (Users)                            |

**Service Check**

|Check Item |Check Content |
| ------------------------------- | --------------------------------------- |
| Firewall (Firewall)             | Enabled on Startup (Enabled)            |
|                                  | Status (Status)                          |
|                                  | Open Ports (OpenPorts)                  |
| Listening Check (Listening)      | Listening Check (Listening)              |
| Services Enabled on Startup (EnableService) | Services Enabled on Startup (EnableService) |
| SSH Connection Info (SSH)        | Enabled on Startup (Enabled)            |
|                                  | Status (Status)                          |
|                                  | Protocol (Protocol)                      |
|                                  | Allow Root User Login (PermitRootLogin) |
| Running Services (RunningService) | Running Services (RunningService)       |

**Resource Consumption**

|Check Item |Check Content |
| --------------------- | --------------------- |
| CPU Consumption (CPUTop)  | CPU Consumption (CPUTop)  |
| Memory Consumption (MemoryTop) | Memory Consumption (MemoryTop) |

**Disk IO Test** (default disabled)

The following check content is only displayed on the front end. Due to the limited display on the front end, if you need more check content, you can open the JSON file to view.

|Check Item |Check Content |
| -------- | ------------------------------------- |
| Basic Info   | fio Version (fio version)           |
|              | Test Time (time)                    |
|              | Test Parameters (global options)     |
| Job          | Job Name (jobname)                  |
|              | Directory (directory)                |
|              | Elapsed Time (elapsed)               |
|              | Filesystem Block Size (bs)          |
|              | Total File Size (size)              |
|              | Read/Write Mode (rw)                |
|              | Read/Write Mix (rwmixread)          |
|              | Runtime (runtime)                    |
|              | IO Read Data Volume (io_kbytes)     |
|              | Read Bandwidth (bw)                 |
|              | Read Throughput (iops)               |
|              | Total IO Read Count (total_ios)      |
|              | IO Write Data Volume (io_kbytes)    |
|              | Write Bandwidth (bw)                 |
|              | Write Throughput (iops)              |
|              | Total IO Write Count (total_ios)     |
|              | Average IO Queue Depth Statistics (iodepth_level) |
| CPU Status   | User CPU Usage Rate (usr_cpu)       |
|              | Kernel CPU Usage Rate (sys_cpu)     |
|              | Context Switch Count During Test (ctx) |
| Disk Status  | Disk Name (name)                     |
|              | Read IO Request Count (read_ios)    |
|              | Write IO Request Count (write_ios)  |
|              | Total Time Spent in Disk Queue (in_queue) |
|              | Disk Utilization (util)              |

**Network IO Test** (default disabled)

|Check Item |Check Content |
| -------- | -------------- |
| Basic Info   | Listening Port Info       |
| Test Result   | Data Transfer Amount Per Second |

### Check Steps

Currently, server checks are performed in three ways:

- Check the current server.
- Based on the server information in the toml file, connect to the server via SSH to perform the check.
- Use an agent to perform the server check.

Server checks currently support using check files to control whether to check certain items. For details, you can refer to the example in the yascheck.toml file; modifying certain check item parameters to ignore will allow you to choose whether to check.

#### Current Server Check

For the current server check, upon successful execution, a summary.json file and a JSON file containing the server check results will be generated. The server check result file can be customized with a name; if a custom name is not specified, it defaults to yascheck-local-SCN.json.

```bash
$ yasboot collection host
```

You can customize the output folder and filename. For instance, generating a report file in the /home/yashan/check directory, with the server check filename as yashan1.json.

```bash
$ yasboot collection host -o /home/yashan/check/ -n yashan1
```

Specify to generate only a JSON report or generate both HTML and JSON reports (specifying to generate an HTML report will also generate a JSON report simultaneously).

```bash
$ yasboot collection host -F json
$ yasboot collection host -F html
```

When specifying the result to be HTML, three files will be generated.

|File |Description |
| ----------------------- | ------------------------------------------------------------ |
| summary.json            | A report summary file containing scores and integrated check warning items; this filename cannot be changed. |
| yascheck-local-xxx.json | This file contains the check results, and its name can be customized through the --name parameter. |
| yasreport.html          | This file contains the front-end display of the check report and results; this filename cannot be changed. |

Use the yascheck.toml file for checks.

```bash
$ yasboot collection host -F html -t yascheck.toml
```

Use yascheck.toml to generate a front-end result report and name it yashan2, output it to the /home/yashan/check directory.

```bash
$ yasboot collection host -F html -t yascheck.toml -o /home/yashan/check -n yashan2
```

#### SSH Connect to the Server and Perform Checks

Connect via SSH to the servers recorded in the toml file and perform checks, generating server check files corresponding to the number of servers and a summary file summary.json.

```bash
$ yasboot collection host --host hosts.toml
```

Specify to generate an HTML report.

```bash
$ yasboot collection host --host hosts.toml -F html
```

Use a custom yascheck.toml for checks.

```bash
$ yasboot collection host --host hosts.toml -F html -t yascheck.toml
```

#### Use yasagent to Perform Server Checks

Using yasagent to perform server checks will generate a xxx.tar.gz compressed file, with the folder contents as mentioned above.

```bash
$ yasboot collection host -c yashan
```

If you only need to check one server, you can specify the hostid of that server.

```bash
$ yasboot collection host -c yashandb --host host0001
```

Specify to generate an HTML report.

```bash
$ yasboot collection host -c yashandb -F html
```

Use a custom yascheck.toml for checks.

```bash
$ yasboot collection host -c yashandb -F html -t yascheck.toml
```

## collection sql

This command is used to execute and collect SQL on specified yasdb nodes and generate reports.

|Option |Meaning |
| -------------------- | ------------------------------------------------------------ |
| *-c,--cluster*         | The cluster name of YashanDB                             |
| *\-F,--format*         | The format of the SQL collection results, options are json and html |
| *\-o,--output*         | The output directory for SQL collection results and reports |
| *\-n,--name*           | The identifier name for SQL collection results and reports |
| *\-t,--toml*           | Use the configuration items in the toml file, default is conf/sqlcollect.toml |
| *\-a,--addr*           | Specify the address of the node, can be `IP:port` or the path of YASDB_DATA |
| *\-p,--sys-password*   | sys user password                                        |
| *\--node-ids*          | The node IDs for SQL collection; can enter multiple and separate with commas `,`, default is `1-1` (can use `all` to collect from all nodes) |
| *--disable*            | Suppress task progress output                            |
| *-d,--child*           | Show information on child task execution                |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
# Execute SQL collection on the local machine
$ yasboot collection sql --addr 127.0.0.1:1688 --sys-password password

# Specify YASDB_DATA path and execute SQL collection on the local machine with passwordless access. Local passwordless access requires adding the user group: YASDBA
$ yasboot collection sql --addr ${YASDB_DATA} -F html

# Use the configuration items from the toml file for collection
$ yasboot collection sql -t sqlcollect.toml --addr 127.0.0.1:1688 --sys-password password

# Execute server check and generate the front-end report output to the specified directory
$ yasboot collection sql -F html --output /home/yashan/check --addr ${YASDB_DATA}

# Execute SQL collection on cluster node 1-1
$ yasboot collection sql -c yashandb --sys-password password

# Execute SQL collection on cluster nodes 2-1 and 3-2
$ yasboot collection sql -c yashandb --sys-password password --node-ids 2-1,3-2

# Execute SQL collection on all nodes in the cluster
$ yasboot collection sql -c yashandb --sys-password password --node-ids all
```

## collection gstack

This command is used to collect yasdb's gstack information on the server.

> **Note**:
>
> gstack needs to be installed on the corresponding server by the user, and the executable must be correctly located when executing commands via SSH.
>
> If it is confirmed to be installed but cannot be recognized, please check the configuration of the environment variable PATH.

|Option |Meaning |
| -------------- | ------------------------------ |
| *-c,--cluster*   | The cluster name of YashanDB        |
| *\-o,--output*   | The output directory for gstack collection results and reports |
| *\-n,--name*     | The identifier name for gstack collection results and reports |
| *--disable*      | Suppress task progress output        |
| *-d,--child*     | Show information on child task execution |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
# Execute gstack collection on the local machine
$ yasboot collection gstack

# Specify YASDB_DATA path and execute SQL collection on the local machine with passwordless access. Local passwordless access requires adding the user group: YASDBA
$ yasboot collection gstack --output /home/yashan/check -n yashan

# Check all yasdb processes on the agent server
$ yasboot collection gstack -c  yashan
```

## collection all

This command is used to collect server, SQL, gstack, disk IO, and network information in one go. It only supports collection through yasagent.

|Option |Meaning |
| --------------------- | ------------------------------------------------------------ |
| *-c,--cluster*            | The cluster name of YashanDB                             |
| *\-F,--format*            | The format of the server check results, options are json and html |
| *\-o,--output*            | The output directory for server check results and reports |
| *\-n,--name*              | The identifier name for server check results and reports |
| *\-y,--yascheck-toml*     | Use configuration items in the toml file; please refer to [Server Check Configuration File](../Configuration Files/Server Check Configuration File) |
| *\-s,--sql-toml*          | Use configuration items in the toml file                  |
| *\--node-ids*             | The node IDs for SQL collection; separate multiple IDs with commas `,`, default is `1-1` (can use `all` to collect from all nodes) |
| *\-p,--sys-password*      | sys user password                                        |
| *\--gstack*               | Whether to collect gstack information, default is not to collect |
| *--disable*               | Suppress task progress output                            |
| *-cl,--cluster-log*       | Collect all log information and the database configuration file from all nodes in the database cluster |
| *-cls,--cluster-log-start* | Start time for collecting database log information, default is midnight of the current day |
| *-cle,--cluster-log-end*  | End time for collecting database log information, default is the current time |
| *-f, --force*             | Collect log information without confirmation of start and end time |
| *-d,--child*              | Show information on child task execution                |
| *--no-download*           | Do not download locally (hidden parameter)              |
| *--check-module*          | Check modules, such as SQL, GSTACK, or HOST, separate multiple modules with commas `,` (hidden parameter) |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
yasboot collection all -c yashandb --sys-password password -d -F html
```

Collect trace and other database log information.
```shell
yasboot collection all -c yashandb --sys-password password -d -cl 
```
