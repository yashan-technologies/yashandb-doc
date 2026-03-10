## host info

This command is used to display the current server's environmental information.

|Option |Meaning |
| ------------- | -------------------------- |
| *-c,--cpu*      | Collect CPU information of the server |
| *\-d, --disk*   | Collect disk information of the server |
| *\-m, --mem*    | Collect memory information of the server |
| *\-n, --net*    | Collect network information of the server |
| *\-a, --all*    | View all information of the above options |

***Example***

```shell
# Basic information of the server
$ yasboot host info
{
  "host": {
    "hostname": "localhost.localdomain",
    "uptime": 8676,
    "bootTime": 1678325128,
    "procs": 261,
    "os": "linux",
    "platform": "centos",
    "platformFamily": "rhel",
    "platformVersion": "7.6.1810",
    "kernelVersion": "3.10.0-957.el7.x86_64",
    "kernelArch": "x86_64",
    "virtualizationSystem": "",
    "virtualizationRole": "",
    "hostid": "e6800d7f-5399-4172-ac8a-661764ed5adc"
  }
}

# Other usages, for example, to view memory, disk, etc. information
$ yasboot host info -c -d -m -n

# View all information at once
$ yasboot host info -a
```

## host check

This command is used to check whether the specified server meets the environmental requirements for deploying the database cluster.

|Option |Meaning |
| --------------- | ------------------------------------------- |
| *-c, --cluster*     | The cluster name of YashanDB (mandatory parameter) |
| *--host-id*         | The ID of the server to check (mandatory parameter) |
| *--ce-disk-path*    | Path for YAC shared storage (hidden parameter) |
| *--install-path*     | The installation path of YashanDB on the server (hidden parameter) |
| *--version*         | Version information (hidden parameter)             |
| *-f, --force*       | Force check (hidden parameter)                      |
| *--openssl*         | Check openssl information (hidden parameter)       |
| *--disk-found-path* | YAC disk discovery path (hidden parameter) |
| *--paths*           | Paths that need to be validated for privilege (hidden parameter) |

***Example***

```shell
$ yasboot host check -c yashandb --host-id host0001
```

## host add

This command is used to install software packages on newly added remote servers, in tar.gz format.

|Option |Meaning |
| ---------------- | ------------------------------------ |
| -c,--cluster       | Cluster name (mandatory parameter)       |
| -i,--install-pkg   | Local absolute path of the software package file (deprecated parameter) |
| *--plugin*         | Local path of the plugin package file (deprecated parameter) |
| -f, --force        | Ignore errors and force installation, default is false |
| -t,--toml          | Configuration file with server-related information to install the package (mandatory parameter) |
| *-d, --child*      | Display task and sub-task information |
| --disable          | Hide task progress display          |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) (enabled by default after installation) is activated, no password needs to be specified   |
| *--deps*           | Local path of the dependency package file (hidden parameter) |

***Example***

```shell
$ yasboot host add -c yashandb -t hosts_add.toml -d
```

## host cgroup create

This command is used to create a database resource management cgroup directory in the Standalone Deployment (non-cascade backup) and ISC Distributed Cluster Deployment environments of YashanDB.

|Option |Meaning |
| --------------------- | ------------------------------------------------------------ |
| *-c,--cluster*         | Cluster name (mandatory parameter)                              |
| *--host-id*            | Server ID, which can be queried using the `yasboot cluster status` command; defaults to all servers where the database is deployed |
| *-su,--sudo-username*  | SSH user with sudo privilege                                   |
| *-sp,--sudo-password*  | Password for the SSH user with sudo privilege                 |
| *--port*               | Server SSH port, default is 22                                  |
| *--cgroup-path*        | Resource management cgroup directory, default is `/sys/fs/cgroup` |
| *--disable*            | Hide task progress output                                       |
| *-d,--child*           | Display information about sub-task execution                    |

***Example***

```shell
# Create resource management cgroup directories for all servers in the environment
$ yasboot host cgroup create -c yashandb --sudo-username root --sudo-password ******

# Create a resource management cgroup directory for host0002
$ yasboot host cgroup create -c yashandb --sudo-username root --sudo-password ****** --host-id host0002
```

> **Note**:
>
> If resource usage groups or resource plan instructions have been created before executing this command, the database needs to be restarted to take effect.

## host cgroup remove

This command is used to remove the database resource management cgroup directory. After successfully executing this command, the server needs to be restarted to take effect.

Applicable only to Standalone Deployment (non-cascade backup) and ISC Distributed Cluster Deployment environments of YashanDB.

|Option |Meaning |
| --------------------- | ------------------------------------------------------------ |
| *-c,--cluster*         | Cluster name (mandatory parameter)                              |
| *--host-id*            | Server ID, which can be queried using the `yasboot cluster status` command; defaults to all servers where the database is deployed |
| *-su,--sudo-username*  | SSH user with sudo privilege                                   |
| *-sp,--sudo-password*  | Password for the SSH user with sudo privilege                 |
| *--port*               | Server SSH port, default is 22                                  |
| *--disable*            | Hide task progress output                                       |
| *-d,--child*           | Display information about sub-task execution                    |

***Example***

```shell
# Remove resource management cgroup directories for all servers in the environment
$ yasboot host cgroup remove -c yashandb --sudo-username root --sudo-password ******

# Remove the resource management cgroup directory for host0002
$ yasboot host cgroup remove -c yashandb --sudo-username root --sudo-password ****** --host-id host0002
```

## host auth add

This command is used to enable OS authentication for the current user.

|Option |Meaning |
| ---------------- | ------------------------------------ |
| *-c, --cluster*    | Cluster name (mandatory parameter)                |
| *-su, --sudo-username* | SSH username with sudo privilege (used to execute commands requiring sudo privilege, such as creating the YASDBA group and adding users to the YASDBA group) |
| *-sp, --sudo-password* | Password for the SSH user with sudo privilege |
| *-N, --nopassword* | SSH passwordless login              |
| *--port*           | Server SSH connection port          |
| *--host-ids*       | IDs of deployed servers, separated by commas for multiple servers |
| *-w, --nowait*     | Do not wait for the command execution result after running |
| *-d, --child*      | Display task and sub-task information |
| *--disable*        | Hide task progress display          |

***Example***

```shell
$ yasboot host auth add -c yashandb -su yashan -sp password
```

## host auth remove

This command is used to remove passwordless login for the OS user on all deployed servers.

|Option |Meaning |
| ---------------------- | ------------------------- |
| *-c, --cluster*          | Cluster name (mandatory parameter) |
| *-su, --sudo-username*   | SSH username with sudo privilege |
| *-sp, --sudo-password*   | Password for the SSH user with sudo privilege |
| *-N, --nopassword*       | SSH passwordless login       |
| *--port*                 | Server SSH connection port    |
| *--host-ids*             | IDs of deployed servers, separated by commas for multiple servers |
| *-w, --nowait*           | Do not wait for the command execution result after running |
| *-d, --child*            | Display task and sub-task information |
| *--disable*              | Hide task progress display    |

```shell
$ yasboot host auth remove -c yashandb -su yashan -sp password
```

## host remove

This command is used to uninstall the installed YashanDB server from idle remote servers.

|Option |Meaning |
| ---------------- | ------------------------------------ |
| *-c,--cluster*     | Cluster name (mandatory parameter)        |
| *--host-ids*       | IDs of deployed servers, separated by commas for multiple servers |
| *--ip*             | IPs of deployed servers, separated by commas for multiple servers |
| *-t, --toml*       | Configuration file to uninstall via SSH, e.g., hosts.toml |
| *-f,--force*       | Force uninstall flag; prompts for confirmation if this parameter is not used |
| *-w, --nowait*     | Do not wait for the command execution result after running |
| *-d, --child*      | Display task and sub-task information  |
| *--disable*        | Hide task progress display              |
| *--with-unconnected-host* | If the server is no longer reachable, use this parameter to force delete the server. Only delete the server data in yasom; the yasagent process and installation package will be retained |

***Example***

```shell
$ yasboot host remove -c yashandb --host-ids host0002
```
