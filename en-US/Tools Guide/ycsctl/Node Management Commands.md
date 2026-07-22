### ycsctl start ycs

This command is used to start YCS on the current server and the resources it manages. Whether YashanDB database is started simultaneously with YCS is controlled by the `AUTO_START` parameter.

The command format and parameter descriptions are as follows:

```shell
ycsctl start ycs [-startoption start_options] [-p wallet_password]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
|  -startoption<br />-m | NOMOUNT, MOUNT, or OPEN | Optional parameter. Specifies the default startup phase option for the database instance. If omitted, the default value specified by the `ycsctl add database` command will be used.  |
| -p | wallet_password | Optional parameter. Specifies the [wallet password](../../Product Security/Encryption/Storage Encryption/Key Management) for key management of the database instance. <br />When there are existing encrypted objects in a YAC/Distributed Cluster and you need to start the database directly to MOUNT or OPEN mode, it must be specified; otherwise, the database instance will fail to start.  |



>**Note**:
>
> Please use the `-startoption` option to specify the startup mode. The `-m` option is deprecated and retained only for backward compatibility.



### ycsctl start instance

This command is used to start the database instance on the current server. 

You must ensure that YCS has already been started before executing this command.

In a YAC/Distributed Cluster Deployment CDB, the CDB root is started first, and then whether to start the PDB is determined by the startup strategy (specified by the `-policy` parameter of the `ycsctl add pdb` command).

The command format and parameter descriptions are as follows:

```shell
ycsctl start instance [-startoption start_option] [-p wallet_password]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
|  -startoption<br />-m | NOMOUNT, MOUNT, or OPEN | Optional parameter. Specifies the default startup phase option for the database instance. If omitted, the default value specified by the `ycsctl add database` command will be used.   |
| -p | wallet_password | Optional parameter. Specifies the [wallet password](../../Product Security/Encryption/Storage Encryption/Key Management) for key management of the database instance. <br />When there are existing encrypted objects in a YAC/Distributed Cluster and you need to start the database directly to MOUNT or OPEN mode, it must be specified; otherwise, the database instance will fail to start.  |



>**Note**:
>
> Please use the `-startoption` option to specify the startup mode. The `-m` option is deprecated and retained only for backward compatibility.



### ycsctl stop instance

This command is used to stop the database instance on the current server.  

The command format is as follows:

```shell
ycsctl stop instance
```

### ycsctl start pdb

This command is only applicable to a YAC/Distributed Cluster Deployment CDB, used to start an instance of the target PDB on the specified server. 

You must ensure that the database instance has already been started before executing this command.

The command format and parameter descriptions are as follows:

```shell
ycsctl start pdb -db db_unique_name -pdb pdb_name [-node nodename_list] [-startoption start_options] [-p wallet_password]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -db | db_unique_name | Specifies the unique name of the target database resource for the current operation, which must be an existing database resource.<br />This name can be queried via the `ycsctl show config` command, where the value of the `Database unique name` field represents this name.   |
| -pdb | pdb_name | Specifies the name of the PDB to be started. |
|  -startoption<br />-m | NOMOUNT, MOUNT, or OPEN | Optional parameter. Specifies the default startup phase option for the database instance. If omitted, the default value specified by the `ycsctl add pdb` command will be used.  |  |  |
| -node | nodename_list | Optional parameter. Specifies the database instances (i.e., servers) on which the PDB should run after startup. Multiple instance names are separated by commas (`,`). If omitted, the PDB will run on all instances. <br />This name can be queried via the `ycsctl show config` command, where the value of the `Node name` field represents the instance node name.<br />
In YAC/Distributed Cluster Deployment, each PDB can run multiple instances (i.e., 0-1 instances per server) to concurrently read and write the same data. In practice, you can comprehensively consider server resource conditions and availability requirements for certain PDBs to balance the load across each PDB's instances.
 |
| -p | wallet_password | Optional parameter. Specifies the [wallet password](../../Product Security/Encryption/Storage Encryption/Key Management) for key management of the database instance. <br />When there are existing encrypted objects in a YAC/Distributed Cluster and you need to start the database directly to MOUNT or OPEN mode, it must be specified; otherwise, the database instance will fail to start.  |  |  |



>**Note**:
>
> Please use the `-startoption` option to specify the startup mode. The `-m` option is deprecated and retained only for backward compatibility.



***Example***

```shell
# Start PDB1 on the host0001 server
$ ycsctl start pdb -db yashandb -pdb pdb1 -node host0001 -startoption open
```

### ycsctl stop pdb

This command is only applicable to a YAC/Distributed Cluster Deployment CDB, used to stop the instance of the target PDB on the current server. 

You must ensure that the database instance has already been started before executing this command.

The command format and parameter descriptions are as follows:

```shell
ycsctl stop pdb -db db_unique_name -pdb pdb_name
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -db | db_unique_name | Specifies the unique name of the target database resource for the current operation, which must be an existing database resource.<br />This name can be queried via the `ycsctl show config` command, where the value of the `Database unique name` field represents this name.   |
| -pdb | pdb_name | Specifies the name of the PDB to be stopped. |

### ycsctl stop ycs

This command is used to stop YCS on the current server and the resources it manages.  

The command format is as follows:

```shell
ycsctl stop ycs
```

### ycsctl start osw

This command is used to start the OS Watcher for the current YCS.  

The command format is as follows:

```shell
ycsctl start osw
```

### ycsctl stop osw

This command is used to stop the OS Watcher for the current YCS.  

> **Note**: 
>
> For detailed information on YCS OS Watcher, please refer to [Operating System Load Monitoring](../../Database Administration/Operation Monitoring/YCS OS Watcher).

The command format is as follows:

```shell
ycsctl stop osw
```

### ycsctl start vip

This command is used to start VIP resources on the current YCS.  

The command format and parameter descriptions are as follows:

```shell
ycsctl start vip [-n node_name]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -n | node_name | Optional parameter. Specifies the name of the instance node for which the VIP resource should be started. If omitted, the VIP resource of the current node will be started by default. |

### ycsctl stop vip

This command is used to stop the acquired VIP resources on the current YCS.  

The command format and parameter descriptions are as follows:

```shell
ycsctl stop vip [-n node_name]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -n | node_name | Optional parameter. Specifies the name of the instance node for which the VIP resource should be stopped. If omitted, the VIP resource of the current node will be stopped by default. |

### ycsctl relocate vip

This command is used to relocate specified VIP resources to other YCS nodes.

The command format and parameter descriptions are as follows:

```shell
ycsctl relocate vip -n home_node [-t node_name]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -n | home_node | Specifies the name of the instance node to which the target VIP resource belongs. The mapping between instance nodes and VIPs can be viewed via `ycsctl show config`. |
| -n | node_name | Optional parameter. Specifies the name of the target node to which the VIP resource will be relocated. If omitted, YCS will automatically relocate the VIP resource according to high availability and round-robin principles. |


### ycsctl get

This command is used to view the values of [YCS node-level parameters](../../Database Administration/Cluster Management/Cluster Parameter Configuration.md#Node-Level) on the current server.

The command format and parameter descriptions are as follows:

```shell
ycsctl get [parameter_name]
```

|Parameter Value | Description          |
| ----------- | ------------------------------------------------------------ |
|  parameter_name    | Specifies the name of the parameter to be viewed; case-insensitive.  |

### ycsctl set [parameter_name] value

This command is used to online set the values of [YCS node-level parameters (non-readonly parameters)](../../Database Administration/Cluster Management/Cluster Parameter Configuration.md#Node-Level) on the current server.

>**Note**:
>
> For the `AUTO_START` parameter, this command only modifies its value in the configuration file; actual effect requires restarting YCS.

The command format and parameter descriptions are as follows:

```shell
ycsctl set [parameter_name] value
```

|Parameter Value | Description          |
| ----------- | ------------------------------------------------------------ |
| parameter_name    | Specifies the name of the parameter to be modified; case-insensitive.  |
| value         | Specifies a value within the allowable range for the corresponding parameter.     |


### ycsctl status

This command is used to view the topology status of the current cluster, displayed in a horizontal table format by default, with the number of servers displayed equal to the number of deployed servers. 

The command format and parameter descriptions are as follows:

```shell
ycsctl status [-v|vertical]
```

| Parameter Option   | Description          |
| ---------- | ------------------------------------------------------------ |
| -v<br/>-vertical |  Optional parameter. Indicates displaying the topology status in a vertical table format. |

***Example***

```shell
$ ycsctl status
+--------------+-------------------+-----------------+---------------------------------------------+
| Self Host ID | Cluster Master ID | YasFS Master ID |                Active Host Count            |
+--------------+-------------------+-----------------+---------------------------------------------+
|1             |2                  |2                |2                                            |
+--------------+-------------------+-----------------+---------------------------------------------+
+---------+---------+---------+---------+-------------+--------------------+--------------------+-----------+-----------+
| Host ID | Target  |   YCS   |   YFS   |     VIP     |      DB_NAME       |      PDB_NAME      | DB_STATE  | IS_MASTER |
+---------+---------+---------+---------+-------------+--------------------+--------------------+-----------+-----------+
|1        |online   |online   |online   |host1.online |yashandb            |cdb$root            |online     |FALSE      |
|         |         |         |         |             |                    +--------------------+-----------+-----------+
|         |         |         |         |             |                    |pdb1                |online     |FALSE      |
|         |         |         |         |             |                    +--------------------+-----------+-----------+
|         |         |         |         |             |                    |yashancdb_pdb2      |online     |FALSE      |
|         |         |         |         |             |                    +--------------------+-----------+-----------+
|         |         |         |         |             |                    |yashancdb_pdb3      |online     |TRUE       |
+---------+---------+---------+---------+-------------+--------------------+--------------------+-----------+-----------+
|2        |online   |online   |online   |host1.online |yashandb            |cdb$root            |online     |TRUE       |
|         |         |         |         |             |                    +--------------------+-----------+-----------+
|         |         |         |         |             |                    |pdb1                |online     |TRUE       |
|         |         |         |         |             |                    +--------------------+-----------+-----------+
|         |         |         |         |             |                    |yashancdb_pdb2      |online     |TRUE       |
|         |         |         |         |             |                    +--------------------+-----------+-----------+
|         |         |         |         |             |                    |yashancdb_pdb3      |offline    |FALSE      |
+---------+---------+---------+---------+-------------+--------------------+--------------------+-----------+-----------+
+---------------------------------------+---------------------------------------+---------------------------------------+
|SCAN VIP 1: host1.online               |SCAN VIP 2: host2.online               |                                       |
+---------------------------------------+---------------------------------------+---------------------------------------+

$ ycsctl status -v
[cluster_information]
Self Host ID: 1
Cluster Master ID: 2
YasFS Master ID: 2
Active Host Count: 2
SCAN VIP 1: host1.online
SCAN VIP 2: host2.online

[host_information.host1]
Host ID: 1
Target: online
State: online
YasFS: online
VIP: host1.online
[database_information.yashancdb]
DB State: online
[database_information.pdb1]
DB State: online
[database_information.yashancdb_pdb2]
DB State: online
[database_information.yashancdb_pdb3]
DB State: online
[database_information.pdb_add2]
DB State: online

[host_information.host2]
Host ID: 2
Target: online
State: online
YasFS: online
VIP: host1.online
[database_information.yashancdb]
DB State: online(MASTER)
[database_information.pdb1]
DB State: online(MASTER)
[database_information.yashancdb_pdb2]
DB State: online(MASTER)
[database_information.yashancdb_pdb3]
DB State: offline
```


The following describes each configuration item:

| Field Name       |Meaning                  |
| ------------ | -------------------------------- |
| Self Host ID | The ID of the current server. |
| Cluster Master ID | The ID of the master server corresponding to the YCS service. |
| YasFS Master ID | The ID of the master server corresponding to the YFS service. |
| Active Host Count | The current number of active servers in the cluster. |
| Host ID | Server ID. |
| Target | When the server is started, it indicates whether the database instance is included to start with YCS, according to the AUTO_START parameter; when AUTO_START=ALWAYS, the Target is online; when AUTO_START=NEVER, the Target is offline.<br />When the server is stopped, it is always offline. |
| YCS | The current state of the YCS service. |
| YFS | The current state of the YFS service. |
| VIP | The current status of the VIP resource deployed on this YCS node, formatted as: `<Acquired Node Name.Running State (IP Address:Port)>`. |
| DB_NAME | The name of the database resource. |
| PDB_NAME | The name of PDBs. |
| DB_STATE | The current state of the database instance. |
| IS_MASTER | Whether it is a primary database. |
| SCAN VIP n | Current SCAN VIP information and operational status of the YCS node, formatted as: `<node name where the SCAN VIP is running>.operational status>`. |



### ycsctl status pdb

This command is used to view information about the target PDB. 

The command format and parameter descriptions are as follows:

```shell
ycsctl status pdb -db db_unique_name -pdb pdb_name
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -db | db_unique_name | Specifies the unique name of the target database resource for the current operation, which must be an existing database resource.<br />This name can be queried via the `ycsctl show config` command, where the value of the `Database unique name` field represents this name.   |
| -pdb | pdb_name | Specifies the name of the PDB to be viewed. |

***Example***

```shell
$ ycsctl status pdb -db yashandb -pdb pdb1
+-------------------+---------------------------------+---------------------------+-------------------------------------+
|   Self Host ID    |        Cluster Master ID        |      YasFS Master ID      |          Active Host Count          |
+-------------------+---------------------------------+---------------------------+-------------------------------------+
|1                  |1                                |1                          |2                                    |
+-------------------+---------------------------------+---------------------------+-------------------------------------+
+---------+---------+---------+---------+-------------+--------------------+--------------------+-----------+-----------+
| Host ID | Target  |   YCS   |   YFS   |     VIP     |      DB_NAME       |      PDB_NAME      | DB_STATE  | IS_MASTER |
+---------+---------+---------+---------+-------------+--------------------+--------------------+-----------+-----------+
|1        |online   |online   |online   |             |yashandb            |pdb1                |online     |TRUE       |
+---------+---------+---------+---------+-------------+--------------------+--------------------+-----------+-----------+
|2        |online   |online   |online   |             |yashandb            |pdb1                |online     |FALSE      |
+---------+---------+---------+---------+-------------+--------------------+--------------------+-----------+-----------+
```



The following describes each configuration item:

| Field Name       |Meaning                  |
| ------------ | -------------------------------- |
| Self Host ID | The ID of the current server. |
| Cluster Master ID | The ID of the master server corresponding to the YCS service. |
| YasFS Master ID | The ID of the master server corresponding to the YFS service. |
| Active Host Count | The current number of active servers in the cluster. |
| Host ID | Server ID. |
| Target | When the server is started, it indicates whether the database instance is included to start with YCS, according to the AUTO_START parameter; when AUTO_START=ALWAYS, the Target is online; when AUTO_START=NEVER, the Target is offline.<br />When the server is stopped, it is always offline. |
| YCS | The current state of the YCS service. |
| YFS | The current state of the YFS service. |
| VIP | The current status of the VIP resource deployed on this YCS node, formatted as: `<Acquired Node Name.Running State (IP Address:Port)>`. |
| DB_NAME | The name of the database resource. |
| PDB_NAME | The name of PDBs. |
| DB_STATE | The current state of the database instance. |
| IS_MASTER | Whether it is a primary database. |
| SCAN VIP n | Current SCAN VIP information and operational status of the YCS node, formatted as: `<node name where the SCAN VIP is running>.operational status>`. |



### ycsctl show parameter

This command is used to view the configuration of all [YCS node-level parameters](../../Database Administration/Cluster Management/Cluster Parameter Configuration.md#Node-Level) on the current server.

The command format is as follows:

```shell
ycsctl show parameter
```

### ycsctl show fence

This command is used to check whether all servers in the cluster have privilege read and write access to the data disk, requiring the YCSRA process to be online to provide query service. 

The command format is as follows:

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

The following describes each configuration item:

| Field Name       |Meaning                  |
| ------------ | -------------------------------- |
| Fence Type | The I/O fencing type used by the current cluster. |
| Host ID | Server ID. |
| Permission | Privilege for the server to access the data disk, including: ALLOW (read/write allowed), BAN (write not allowed), or UNDEFINED (undefined). |

### ycsctl query disk

This command is used to view the information of the SYSTEM disk group.  

The command format is as follows:

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

The following describes each configuration item:

| Field Name       |Meaning                  |
| ------------ | -------------------------------- |
| ID | The sequence number of the disk in the SYSTEM DiskGroup. |
| STATUS | The status of the disk in the SYSTEM DiskGroup. |
| PATH | The path of the disk in the SYSTEM DiskGroup. |
| DG | The name of the SYSTEM DiskGroup. |
