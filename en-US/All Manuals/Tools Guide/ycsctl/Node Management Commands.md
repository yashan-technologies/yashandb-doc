### ycsctl start ycs

This command is used to start YCS on the current server and the resources it manages (the startup of YashanDB resources is controlled by the AUTO_START parameter; the database instance startup mode is controlled by the optional parameter -m). The command format is as follows:

```shell
ycsctl start ycs [-m mode] [wallet_password]
```

**mode**

Specify the startup mode for the database instance. Valid values are NOMOUNT, MOUNT, or OPEN; the default is OPEN if omitted.

**wallet_password**

Specify the [wallet password](../../Product Security/Encryption/Storage Encryption/Key Management) for key management of the database instance. When there are existing encrypted objects in a YAC and you need to start the database directly to MOUNT or OPEN mode, it must be specified; otherwise, the database instance will fail to start.

### ycsctl start instance

This command is used to start the database instance on the current server, with the optional parameter -m specifying the database startup mode. The command format is as follows:

```shell
ycsctl start instance [-m mode] [-p wallet_password]
```

You must ensure that YCS has already been started before executing this command.

**mode**

Specify the startup mode for the database instance. Valid values are NOMOUNT, MOUNT, or OPEN; the default is OPEN if omitted.

**wallet_password**

Specify the [wallet password](../../Product Security/Encryption/Storage Encryption/Key Management) for key management of the database instance. When there are existing encrypted objects in a YAC and you need to start the database directly to MOUNT or OPEN mode, it must be specified; otherwise, the database instance will fail to start.

### ycsctl stop instance

This command is used to stop the database instance on the current server; the command format is as follows:

```shell
ycsctl stop instance
```

> **Note**: 
>
> - YCS uses scripts to start and stop database instances, and the script is saved at $YASCS_HOME/scripts/. The script name can be queried using the "ycsctl show config" command.
> - The contents of the script to stop the database instance usually are: *yasql* logs into the database instance and executes the "shutdown immediate" command. To enhance security, it is recommended to complete the [OS authentication configuration](../../产品安全/身份标识与鉴别/操作系统认证/配置操作系统认证) and use yasql for [password-free login](../yasql/User Guide for yasql).
> - When deploying a YAC using *yasboot*, the *yasboot* tool automatically completes the above configuration and generates a script for password-free login.

### ycsctl stop ycs

This command is used to stop YCS on the current server and the resources it manages; the command format is as follows:

```shell
ycsctl stop ycs
```

### ycsctl start osw

This command is used to start the OS Watcher for the current YCS; the command format is as follows:

```shell
ycsctl start osw
```

### ycsctl stop osw

This command is used to stop the OS Watcher for the current YCS; the command format is as follows:

```shell
ycsctl stop osw
```

> **Note**: 
>
> For detailed information on YCS OS Watcher, please refer to [Operating System Load Monitoring](../../YashanDB for Cluster/Yashan Cluster Service/Operating System Watcher).

### ycsctl start vip

This command is used to start VIP resources on the current YCS; the command format is as follows:

```shell
ycsctl start vip [-n name]
```

**-n**

The name of the deployment node for the VIP resource to be started. If not specified, the VIP resources configured on the current YCS node will be started by default.

### ycsctl stop vip

This command is used to stop the acquired VIP resources on the current YCS; the command format is as follows:

```shell
ycsctl stop vip [-n name]
```

**-n**

The name of the deployment node for the VIP resource to be stopped. If not specified, all acquired VIP resources on the current YCS node will be stopped by default.

### ycsctl relocate vip

This command is used to migrate specified VIP resources to other YCS nodes; the command format is as follows:

```shell
ycsctl relocate vip -n name1 [-t name2]
```

**-n**

The name of the deployment node for the VIP resource to be migrated, which must be specified.

**-t**

The name of the target node for the migration. If not specified, YCS will automatically migrate based on high availability and balanced distribution.

### ycsctl get

This command is used to view the configuration parameter values on the current server; the command format is as follows:

```shell
ycsctl get [parameter_name]
```

**parameter_name**

Parameter name, derived from the allowed [configuration parameters](../../YashanDB for Cluster/Yashan Cluster Service/YAC Configuration) of YCS. The following parameters can be specified for querying:

- AUTO_START
- LOG_LEVEL
- LOG_NUMBER
- LOG_SIZE
- RESTART_TIMES
- RESTART_INTERVAL
- WAIT_STOP_FIN_TIME
- OSW_AUTO_START
- OSW_INTERVAL
- OSW_FILE_NUM
- OSW_FILE_SIZE

### ycsctl set [parameter\_name] value

This command is used to set the configuration parameters on the current server online; the command format is as follows:

```shell
ycsctl set [parameter_name] value
```

**parameter**

Parameter name, derived from the allowed [configuration parameters](../../YashanDB for Cluster/Yashan Cluster Service/YAC Configuration) of YCS. The following parameters can be specified for setting:

- AUTO_START
- LOG_LEVEL
- LOG_NUMBER
- LOG_SIZE
- RESTART_TIMES
- RESTART_INTERVAL
- WAIT_STOP_FIN_TIME
- OSW_AUTO_START
- OSW_INTERVAL
- OSW_FILE_NUM
- OSW_FILE_SIZE

Note: AUTO_START only modifies the value in the configuration file; the actual effect requires restarting YCS.

**value**

The target parameter value, which must be within the allowed range as specified by YCS [configuration parameters](../../YashanDB for Cluster/Yashan Cluster Service/YAC Configuration).

### ycsctl status

This command is used to view the topology status of the current cluster, displayed in a horizontal table format by default, with the number of servers displayed equal to the number of deployed servers. The command format is as follows:

```shell
ycsctl status [-v|vertical]
```

**-v|vertical**

An optional item. When -v (or vertical) is specified, the topology status will be displayed in a vertical table format.

***Example***

```shell
$ ycsctl status
---------------------------------------------------------------------------------------------
Self Host ID|Cluster Master ID|YasFS Master ID|YasDB Master ID|Active Host Count
---------------------------------------------------------------------------------------------
1            1                 1               1               2
---------------------------------------------------------------------------------------------
Host ID   |Target    |State     |YasFS     |YasDB     |VIP
---------------------------------------------------------------------------------------------
1          online     online     online     online     host1.online
2          online     online     online     online     host2.online
---------------------------------------------------------------------------------------------
SCAN VIP 1: host2.online   SCAN VIP 2: host1.online   SCAN VIP 3: host1.online

$ ycsctl status -v
[cluster_information]
Self Host ID: 1
Cluster Master ID: 1
YasFS Master ID: 1
YasDB Master ID: 1
Active Host Count: 2

[host_information.host1]
Host ID: 1
Target: online
State: online
YasFS: online
YasDB: online
VIP: host1.online

[host_information.host2]
Host ID: 2
Target: online
State: online
YasFS: online
YasDB: online
VIP: host1.online
```

**Self Host ID**

The ID of the current server.

**Cluster Master ID**

The ID of the master server corresponding to the YCS service.

**YasFS Master ID**

The ID of the master server corresponding to the YFS service.

**YasDB Master ID**

The ID of the master server corresponding to the database instance.

**Active Host Count**

The current number of active servers in the cluster.

**Host ID**

Server ID.

**Target**

When the server is stopped, it is always offline. 

When the server is started, it indicates whether the database instance is included to start with YCS, according to the AUTO_START parameter; when AUTO_START=ALWAYS, the Target is online; when AUTO_START=NEVER, the Target is offline.

**State**

The current state of the YCS service.

**YasFS**

The current state of the YFS service.

**YasDB**

The current state of the database instance.

**VIP**

The current status of the VIP resource deployed on this YCS node, formatted as: `<Acquired Node Name.Running State (IP Address:Port)>`.

**SCAN VIP n**

Current SCAN VIP information and operational status of the YCS node, formatted as: `<node name where the SCAN VIP is running>.operational status>`

### ycsctl show parameter

This command is used to view the configuration parameter information of YCS; the command format is as follows:

```shell
ycsctl show parameter
```

***Example***

```shell
$ ycsctl show parameter
Name                Value
------------------- ----------------------
AUTO_START          NEVER
LOG_LEVEL           DEBUG
LOG_NUMBER          10
LOG_SIZE            20MB
RESTART_TIMES       3
RESTART_INTERVAL    30s
WAIT_STOP_FIN_TIME  90s
OSW_AUTO_START      ON
OSW_INTERVAL        20s
OSW_FILE_NUM        20
OSW_FILE_SIZE       20MB
YCR_FILE_NAME       +SYSTEM/ycr
VOTING_FILE_NAME    +SYSTEM/voting
```

### ycsctl show fence

This command is used to check whether all servers in the cluster have privilege read and write access to the data disk, requiring the YCSRA process to be online to provide query service. The command format is as follows:

```shell
ycsctl show fence
```

***Example***

```shell
$ ycsctl show fence
Fence Type: SCSI I/O Fencing
----------------------

Host ID   |Permission
0          ALLOW
1          BAN
2          ALLOW
3          ALLOW
```

**Fence Type**

Specific description of the configured FENCE_TYPE in the cluster.

**Host ID**

Server ID.

**Permission**

Privilege for the server to access the data disk, including: ALLOW (read/write allowed), BAN (write not allowed), UNDEFINED (undefined).

### ycsctl query disk

This command is used to view the information of the SYSTEM disk group; the command format is as follows:

```shell
ycsctl query disk
```

***Example***

```shell
$ ycsctl query disk
ID |STATUS   |PATH                             |DG                              
0  ONLINE    /dev/yfs/sdd                      SYSTEM                          
1  ONLINE    /dev/yfs/sdc                      SYSTEM                          
2  ONLINE    /dev/yfs/sdf                      SYSTEM
```

**ID**

The sequence number of the disk in the SYSTEM disk group.

**STATUS**

The status of the disk in the SYSTEM disk group.

**PATH**

The path of the disk in the SYSTEM disk group.

**DG**

The name of the disk group.

For detailed descriptions of the above parameters, see the [YAC configuration](../../YashanDB for Cluster/Yashan Cluster Service/YAC Configuration) section.