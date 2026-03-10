YCS has implemented a system load monitoring mechanism called OS Watcher, which regularly collects the current load information of the operating system.

## Introduction to OS Watcher

OS Watcher is an embedded tool provided by the YCS server, existing in the form of a thread. During its operation, this thread periodically obtains and saves performance metrics of the operating system by calling system commands (such as iostat, vmstat, etc.) or reading virtual files (such as /proc/meminfo), which helps monitor the resource usage of the operating system. The startup and shutdown methods of the threads related to OS Watcher are simple, consuming few resources, and provide comprehensive information, which can assist in performance analysis and diagnosis.

## Starting and Stopping OS Watcher

By default, OS Watcher starts when YCS starts. Users can configure the auto-start feature through the YCS configuration parameter [OSW_AUTO_START](../集群管理/集群参数配置.html#osw_auto_start) as needed.

Users can also manually start and stop OS Watcher using the *ycsctl* tool, with the command format as follows:

```shell
ycsctl start osw
ycsctl stop osw
```

## Monitoring with OS Watcher

### Monitored Objects

The monitored objects of OS Watcher can be divided into system commands and virtual file reads, supporting a total of 10 monitored objects. The system commands include common performance tools such as iostat and vmstat, while virtual files include commonly accessed information such as /proc/meminfo and /proc/slabinfo.

When OS Watcher starts, it checks the monitored objects one by one to determine whether they are executable or readable, processing only the objects that pass the checks during runtime:

- For system commands, OS Watcher checks the /bin, /usr/bin, and /usr/sbin directories of the operating system in order. If a system command exists in any of the directories mentioned above and has execute privileges, it is considered as passed the check.

- For virtual files, OS Watcher checks the file path. If the file exists and has read privileges, it is also considered as passed the check.

The supported commands are shown in the table below.

|Command Name |Complete Command |Remarks |
| -------------| -------------------- | ----------------------- |
| arp          | arp -an                   | Get the complete ARP buffer of the local machine |
| iostat       | iostat -xk                | Get IO statistics of disk devices, results in KB |
| ip           | ip -s -s addr             | Get address information and statistics for each network interface |
| meminfo      | none                      | Read the /proc/meminfo file to get current memory usage details |
| mpstat       | mpstat -P ALL             | Get statistics for each CPU core |
| nfsiostat    | nfsiostat                 | Get statistics for all currently mounted network file systems |
| ps           | ps -elf --sort=-%cpu,-%mem,-etime &#124; head -n 1000 | Get detailed information of processes running on the current machine, sorted in descending order by CPU usage, memory usage, and running time, and retrieves up to 1000 lines |
| slabinfo     | none                      | Read the /proc/slabinfo file to get current memory allocation details |
| top          | top -b -n 1 &#124; head -n 50 | Get information and statistics of currently running processes, sorted in descending order by CPU usage, and retrieves up to 50 lines |
| vmstat       | vmstat                    | Get details of current system virtual memory usage |

### Monitoring Frequency

By default, the time interval for OS Watcher to collect information is set to one-third of the disk heartbeat timeout (DISK_HB_KEEP_ALIVE), rounded down to the nearest integer. Users can specify the interval as needed through the YCS configuration parameter [OSW_INTERVAL](../集群管理/集群参数配置.html#osw_interval).

## Monitoring Results of OS Watcher

The monitoring results of OS Watcher (i.e., the collected data) will be saved in file format.

### Result File Path

The monitoring results files of OS Watcher will be saved under the ${YASCS_HOME}/diag/osw folder of the corresponding YCS node. Subfolders will be created based on different system command names, writing the results of each command into a file with the filename format `osw-<command>-<timestamp>.dat`.

```shell
# When OS Watcher is stopped
$ ycsctl start osw
Succeed

$ tree ${YASCS_HOME}/diag
/home/yashandb/YASCS_HOME1/diag
├── osw
│   ├── arp
│   │   └── osw-arp-20250101093000.dat
│   ├── iostat
│   │   └── osw-iostat-20250101093000.dat
│   ├── ip
│   │   └── osw-ip-20250101093000.dat
│   ├── meminfo
│   │   └── osw-meminfo-20250101093000.dat
│   ├── mpstat
│   │   └── osw-mpstat-20250101093000.dat
│   ├── nfsiostat
│   │   └── osw-nfsiostat-20250101093000.dat
│   ├── ps
│   │   └── osw-ps-20250101093000.dat
│   ├── slabinfo
│   │   └── osw-slabinfo-20250101093000.dat
│   ├── top
│   │   └── osw-top-20250101093000.dat
│   └── vmstat
│       └── osw-vmstat-20250101093000.dat
└── trace

12 directories, 10 files
```

### Content of Result Files

The specific content of the OS Watcher monitoring result files is the output of the corresponding system command. For example, after collecting data once, the current result file for iostat will additionally contain the following content:
``` shell
###################################################
## OS WATCHER RECORD: 2025-01-01 11:23:15.945831 ##
###################################################
Linux 3.10.0-1160.119.1.el7.x86_64 (AchorBase) 	01/01/2025 	_x86_64_	(8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           1.90    0.00    2.43    0.11    0.00   95.57

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
sdb               0.00    25.69    0.08   71.15     4.84   848.09    23.95     0.01    0.20    0.60    0.20   0.09   0.62
sda               0.00     0.04    0.11    0.67     3.97    38.81   110.75     0.00    1.36    0.53    1.49   0.07   0.01
sdd               0.00     2.16   23.51    1.83  3150.96    21.55   250.35     0.01    0.29    0.28    0.33   0.26   0.66
sdf               0.00     0.00    1.53    0.10   144.66     0.62   178.19     0.00    0.11    0.11    0.08   0.11   0.02
sde               0.00     0.00    2.94    1.10   846.88   153.81   495.22     0.00    0.67    0.78    0.36   0.11   0.05
sdg               0.00     0.00    0.00    0.00     0.15     0.07   282.15     0.00    0.62    0.49    1.71   0.09   0.00
sdh               0.00     0.08    0.08    0.34     2.26    23.83   122.66     0.00    0.47    0.14    0.56   0.08   0.00
sdc               0.00     0.00   16.24    1.13  1398.94     4.79   161.61     0.00    0.14    0.14    0.10   0.13   0.22
dm-0              0.00     0.00    0.11    0.70     3.96    38.81   105.92     0.00    1.36    0.53    1.48   0.08   0.01
dm-1              0.00     0.00    0.00    0.00     0.00     0.00    10.22     0.00   13.21    0.26   15.20   7.30   0.00
dm-2              0.00     0.00    0.08   96.84     4.84   848.09    17.60     0.02    0.17    0.60    0.17   0.06   0.62
```

### Result File Management

OS Watcher supports specifying the maximum number of files that can be saved and the size of individual files, controlled by the YCS configuration parameters [OSW_FILE_NUM](../集群管理/集群参数配置.html#osw_file_num) and [OSW_FILE_SIZE](../集群管理/集群参数配置.html#osw_file_size).

When the size of the currently used file exceeds the limit, OS Watcher will create a new file. If the total number of files exceeds the limit at this time, OS Watcher will delete the oldest files in chronological order until the number meets the standard.

> **Note**: 
>
> The limits on the number and size of data files will apply to each command's subfolder separately.

This document takes the [Typical Deployment](../../安装和升级/安装部署/YashanDB服务端安装（命令行）/共享集群部署) as an example, where data is collected every 20 seconds. The growth rates of file sizes are shown in the table below. The table data is for reference only and should be based on actual conditions.

|File Name |File Growth Rate (MB/h) | 
| -------------| -------- |
| arp          | 0.01~0.1 |
| iostat       | 0.2~0.4  |
| ip           | 0.2~0.4  |
| meminfo      | 0.1~0.2  | 
| mpstat       | 0.1~0.3  |
| nfsiostat    | 0.01~0.1 |
| ps           | 5~10     |
| slabinfo     | 2~4      |
| top          | 0.5~1    |
| vmstat       | 0.04~0.1 |
