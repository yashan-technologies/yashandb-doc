### ycsctl add database

This command is used to add a database resource to the cluster during the deployment phase. This command cannot be used after the cluster deployment is complete. 

>**Note**:
>
> During the installation and deployment of YashanDB, the system internally invokes the operation to add a database, and no manual execution of this command is required.

The command format and parameter descriptions are as follows:

```shell
ycsctl add database -db <db_unique_name> [-dbname <db_name>] [-startoption <start_options>]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -db | db_unique_name | Specifies the unique name of the database resource within the current cluster, consisting of 1 to 64 characters, supporting letters, digits, underscores (_), hash symbols (#), or dollar signs ($). |
| -dbname | db_name | Optional parameter. Specifies the database creation name. If omitted, the default name provided by `-db` will be used. |
| -startoption | NOMOUNT, MOUNT, or OPEN | Optional parameter. Specifies the default startup stage option for the database instance. If omitted, the default is OPEN.<br />When starting the database instance subsequently, if no specific startup option is specified, the value specified here is directly reused as the default value. |

### ycsctl add instance

This command is used to add a database instance to the cluster during the deployment phase. This command cannot be used after the cluster deployment is complete. 

The command format and parameter descriptions are as follows:

```shell
ycsctl add instance -db <db_unique_name> -node <nodename> -instance <instancename> -datahome <db_data_home>
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -db | db_unique_name | Specifies the unique name of the target database resource for the current operation, which must be an existing database resource.<br />This name can be queried via the `ycsctl show config` command, where the value of the `Database unique name` field represents this name.  |
| -node | nodename | Specifies the name of the target instance node for the current operation, which must be an existing node name within the target database resource. |
| -instance | instancename | Specifies the name for the newly added database instance; it must be globally unique within the target database resource. |
| -datahome | db_data_home | Specifies the $YASDB_DATA directory for the database; an absolute path must be specified, for example, `/data/yashan/yasdb_data`. |

### ycsctl start instance

This command is used to start the database instance on the current server. 

You must ensure that YCS has already been started before executing this command.

In a YAC/Distributed Cluster Deployment CDB, the CDB root is started first, and then whether to start the PDB is determined by the startup strategy (specified by the `-policy` parameter of the `ycsctl add pdb` command).

The command format and parameter descriptions are as follows:

```shell
ycsctl start instance [-startoption <startoption>] [-p <password>]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
|  -startoption<br />-m | NOMOUNT, MOUNT, or OPEN | Optional parameter. Specifies the default startup phase option for the database instance. If omitted, the default value specified by the `ycsctl add database` command will be used.   |
| -p | password | Optional parameter. Specifies the [wallet password](../../../Product Security/Encryption/Storage Encryption/Key Management) for key management of the database instance. <br />When there are existing encrypted objects in a YAC/Distributed Cluster and you need to start the database directly to MOUNT or OPEN mode, it must be specified; otherwise, the database instance will fail to start.  |

>**Note**:
>
> Please use the `-startoption` option to specify the startup mode. The `-m` option is deprecated and retained only for backward compatibility.

### ycsctl stop instance

This command is used to stop the database instance on the current server.  

The command format is as follows:

```shell
ycsctl stop instance
```

### ycsctl modify instance

This command is used to modify the YCS instance name. 

The general steps for this operation are as follows:

1. Stop the cluster.
2. Start the [yasfs](../../yasfs) service.
3. Execute this command to modify the YCS instance name.
4. Stop the yasfs service.
5. Start the cluster.

The command format and parameter descriptions are as follows:

```shell
ycsctl modify instance <nodename> -instancename <newname>
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
|            | nodename | Specifies the name of the target instance node for the current operation, which must be an existing node name within the target database resource. |
| -instancename | newname | Specifies the new instance name; it must be globally unique within the target database resource. |

<span id="addpdb" name="addpdb"></span>

### ycsctl add pdb

This command is used to add a PDB resource to the YCS in a YAC/Distributed Cluster Deployment CDB. 

> **Note**:
>
> `-offline` is typically used as an internal command invoked by *yasboot* during YashanDB upgrade. Manual execution by users is **not recommended** unless necessary.

The command format and parameter descriptions are as follows: 

```shell
ycsctl add pdb -db <db_unique_name> -pdb <pdb_name> [-policy <policy>] [-startoption <start_options>] [-offline]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -db | db_unique_name | Specifies the unique name of the target database resource for the current operation, which must be an existing database resource.<br />This name can be queried via the `ycsctl show config` command, where the value of the `Database unique name` field represents this name.   |
| -pdb | pdb_name | Specifies the globally unique name for the new PDB, consisting of 1 to 64 characters, supporting letters, digits, or underscores (_), and must start with a letter. |
| -policy | AUTOMATIC or MANUAL | Optional parameter. Specifies the PDB's startup strategy — whether it follows the CDB root's startup.<br />* AUTOMATIC: Default value — the PDB always starts following the CDB root.<br />* MANUAL: The PDB does not follow the CDB root's startup and must be started manually.<br /><br />Once the PDB's startup strategy is specified, it cannot be directly modified. You must first remove the PDB resource from YCS (using the `ycsctl remove pdb` command), then re-add it (using the `ycsctl add pdb` command) and specify the new startup strategy. |
| -startoption | NOMOUNT, MOUNT, or OPEN | Optional parameter. Specifiesthe default startup stage option for the PDB.  If omitted, the default is OPEN.<br />When starting the PDB subsequently, if no specific startup option is specified, the value specified here is directly reused as the default value. |
| -offline |  | Optional parameter. Used only internally by *yasboot* during YashanDB upgrades; unless absolutely necessary, **must not** be manually specified for execution. |

In a YAC/Distributed Cluster Deployment CDB, the process for adding PDBs through *ycsctl* is as follows, and is performed while the CDB is running.

1. Log in to any database installation server as the installation user and execute the `ycsctl add pdb` command to add a PDB resource to the YCS.

2. Log in to the CDB root and execute the CREATE PLUGGABLE DATABASE statement to add a new PDB, and the PDB name must be consistent.

### ycsctl start pdb

This command is only applicable to a YAC/Distributed Cluster Deployment CDB, used to start an instance of the target PDB on the specified server. 

You must ensure that the database instance has already been started before executing this command.

The command format and parameter descriptions are as follows:

```shell
ycsctl start pdb -db <db_unique_name> -pdb <pdb_name> [-node <node_list>] [-startoption <startoption>] [-p <password>]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -db | db_unique_name | Specifies the unique name of the target database resource for the current operation, which must be an existing database resource.<br />This name can be queried via the `ycsctl show config` command, where the value of the `Database unique name` field represents this name.   |
| -pdb | pdb_name | Specifies the name of the PDB to be started. |
|  -startoption<br />-m | NOMOUNT, MOUNT, or OPEN | Optional parameter. Specifies the startup phase option for the PDB instance. If omitted, the default value specified by the `ycsctl add pdb` command will be used.  |
| -node | node_list | Optional parameter. Specifies the database instances (i.e., servers) on which the PDB should run after startup. Multiple instance names are separated by commas (`,`). If omitted, the PDB will run on all instances. <br />This name can be queried via the `ycsctl show config` command, where the value of the `Node name` field represents the instance node name.<br />In YAC/Distributed Cluster Deployment, each PDB can run multiple instances (i.e., 0-1 instances per server) to concurrently read and write the same data. In practice, you can comprehensively consider server resource conditions and availability requirements for certain PDBs to balance the load across each PDB's instances. |
| -p | password | Optional parameter. Specifies the [wallet password](../../../Product Security/Encryption/Storage Encryption/Key Management) for key management of the database instance. <br />When there are existing encrypted objects in a YAC/Distributed Cluster and you need to start the database directly to MOUNT or OPEN mode, it must be specified; otherwise, the database instance will fail to start.  | 

>**Note**:
>
> Please use the `-startoption` option to specify the startup mode. The `-m` option is deprecated and retained only for backward compatibility.

***Example***

```shell
# Start PDB1 on the host0001 server
$ ycsctl start pdb -db yashandb -pdb pdb1 -node host0001 -startoption open
```

### ycsctl stop pdb

This command is only applicable to a YAC/Distributed Cluster Deployment CDB, used to stop the instance of the target PDB on the specified server. 

You must ensure that the database instance has already been started before executing this command.

The command format and parameter descriptions are as follows:

```shell
ycsctl stop pdb -db <db_unique_name> -pdb <pdb_name> [-node <nodename>]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -db | db_unique_name | Specifies the unique name of the target database resource for the current operation, which must be an existing database resource.<br />This name can be queried via the `ycsctl show config` command, where the value of the `Database unique name` field represents this name.   |
| -pdb | pdb_name | Specifies the name of the PDB to be stopped. |
| -node | nodename | Optional parameter. Specify to stop the PDB instances on the target database instances (i.e., the servers). Multiple instance node names are separated by commas (`,`). If omitted, the PDB instances on all instance nodes will be stopped.<br />This name can be queried via the `ycsctl show config` command, where the value of the `Node name` field represents the instance node name. |

***Example***

```shell
# Stop PDB1 instances on all nodes
$ ycsctl stop pdb -db yashandb -pdb pdb1

# Stop PDB1 instance on host0001 node
$ ycsctl stop pdb -db yashandb -pdb pdb1 -node host0001
```

### ycsctl status pdb

This command is used to view information about the target PDB. 

The command format and parameter descriptions are as follows:

```shell
ycsctl status pdb -db <dbname> -pdb <pdbname> [-v|-vertical]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -db | dbname | Specifies the unique name of the target database resource for the current operation, which must be an existing database resource.<br />This name can be queried via the `ycsctl show config` command, where the value of the `Database unique name` field represents this name.   |
| -pdb | pdbname | Specifies the name of the PDB to be viewed. |
| -v<br/>-vertical | | Optional parameter. Indicates displaying the topology status in a vertical table format. |

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

### ycsctl modify pdb

This command is only applicable to a YAC/Distributed Cluster Deployment CDB, used to modify the startup and policy properties of PDB.

You must ensure that the database instance has already been started and the target PDB has already been stopped before executing this command.

The command format and parameter descriptions are as follows:

```shell
ycsctl modify pdb -db <db_unique_name> -pdb <pdb_name> [-policy <policy>] [-startoption <startoption>] [-offline]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -db | db_unique_name | Specifies the unique name of the target database resource for the current operation, which must be an existing database resource.<br />This name can be queried via the `ycsctl show config` command, where the value of the `Database unique name` field represents this name.   |
| -pdb | pdb_name | Specifies the name of the PDB to be modified. |
| -policy | AUTOMATIC or MANUAL | Optional parameter. Specifies the management policy of the PDB. AUTOMATIC: automatic policy; MANUAL: manual policy. |
| -startoption | NOMOUNT, MOUNT, or OPEN | Optional parameter. Specify startup options for the PDB.  |
| -offline | - | Optional parameter. Specifies whether to execute offline. |

>**Note**:
>
> Please use the `-startoption` option to specify the startup mode. The `-m` option is deprecated and retained only for backward compatibility.

***Example***

```shell
# Modify PDB1 policy to AUTOMATIC
$ ycsctl modify pdb -db yashandb -pdb pdb1 -policy AUTOMATIC

# Modify PDB1 startup option to open
$ ycsctl modify pdb -db yashandb -pdb pdb1 -startoption open
```

### ycsctl remove pdb

This command is used to delete a PDB resource from the YCS in a YAC/Distributed Cluster Deployment CDB. 

You must ensure that the YCS service has already been started before executing this command.

The command format and parameter descriptions are as follows:  

```shell
ycsctl remove pdb -db <db_unique_name> -pdb <pdb_name>
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -db | db_unique_name | Specifies the unique name of the target database resource for the current operation, which must be an existing database resource.<br />This name can be queried via the `ycsctl show config` command, where the value of the `Database unique name` field represents this name.    |
| -pdb | pdb_name | Specifies the name of the PDB resource to be removed, which must be an existing PDB name within the target database resource. |

In a YAC/Distributed Cluster Deployment CDB, the process of deleting PDB through *ycsctl* is as follows: 

1. Stop the target PDB, for example, using the ycsctl stop pdb command, directly connecting to the PDB to execute the SHUTDOWN statement, etc.  

2. Execute the DROP PLUGGABLE DATABASE statement on the CDB root to delete the target PDB. 

3. Execute the ycsctl remove pdb command to delete the target PDB resource from the YCS.  