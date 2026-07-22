### ycsctl create cluster

This command is used to create a new cluster. 

The command format and parameter descriptions are as follows:

```shell
ycsctl create cluster <clustername> [-clusterid <clusterid>] [-o]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
|            | clustername |  Specifies the cluster name, consisting of 4 to 64 characters. Supports letters, digits, hyphens (-), and underscores (_), and must start with a letter.  |
| -clusterid | ID          | Optional parameter. Specifies the unique ID of the cluster, consisting of 1 to 32 hexadecimal characters, used to distinguish the cluster's uniqueness. |
| -o         |         | Optional parameter. Indicates that existing cluster configuration information will be overwritten.<br />Specifying this option will overwrite existing cluster configuration information and cannot be rolled back. Unless absolutely necessary, **DO NOT** specify this option. |

### ycsctl set_ycr

This command is used to offline adjust the values of [YCS cluster-level parameters](../../../Database Administration/Cluster Management/Cluster Parameter Configuration.md#Cluster-Level) in the current cluster.

The general steps for this operation are as follows:

1. Stop the cluster.
2. Start the [yasfs](../../yasfs) service.
3. Execute this command to modify the value of the corresponding parameter.
4. Stop the yasfs service.
5. Start the cluster.

The command format and parameter descriptions are as follows:

```shell
ycsctl set_ycr <key> <value>
```

|Parameter Value | Description          |
| ----------- | ------------------------------------------------------------ |
| key         | Specifies the name of the parameter to be modified; case-insensitive.  |
| value         | Specifies a value within the allowable range for the corresponding parameter.     |

### ycsctl import

This command is used to import a specified configuration script into the current cluster. 

The recommended operational flow is to first stop the entire cluster, then execute the import, and after completion, restart the cluster.

The command format and parameter descriptions are as follows:

```shell
ycsctl import <srcfile> [-f]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
|  | srcfile | Specifies the full path of the configuration script to be imported. |
| -f | | Optional parameter. Indicates forced import. <br />By default, the system will perform node liveness checks before importing to avoid configuration inconsistencies during online import. <br />Specifying this option will skip the node liveness check; use it with caution based on your actual operational scenario. |

### ycsctl export

This command is used to export configuration script information for the current cluster. After export, the file name is fixed as `ycrbackup.sh`.

The command format and parameter descriptions are as follows:

```shell
ycsctl export <destdir>
```

| Parameter Value   | Description          |
| ----------- | ------------------------------------------------------------ |
| destdir | Specifies the storage location for the exported file. |

### ycsctl show config

This command is used to view the current cluster configuration information. 

The command format and parameter descriptions are as follows:

```shell
ycsctl show config
```

***Example***

```shell
$ ycsctl show config
    Cluster name: yashandb, config version: 6
    Cluster id: 47383c354eda3d04e517cdc21862431e
    Network timeout: 30s
    Disk heartbeat keep alive: 30s
    Fence type: I/O Protection Algorithm
    Default resource yasfs: enabled
    Network: 172.16.60.0/24
    Resource SCAN: enabled
      SCAN name: scan.example.com, listening port: 1688
      SCAN VIP: 192.168.1.100, ordinal number: 1
      SCAN VIP: 192.168.1.101, ordinal number: 2
      SCAN VIP: 192.168.1.102, ordinal number: 3
    Resource vip: enabled
    Databases in cluster:
        Database yashandb:
                Resource id: 0
                Database unique name: yashandb
                Database name: yashandb
                Database data directory: /data/yashan/yasdb_data/db-1-1
                Start option: OPEN
                Instances in database:
                        yasdb instance name:yasdb1, yasdb instance id:1
                        yasdb instance name:yasdb2, yasdb instance id:2
                PDBs in database:    // The PDB information section exists only in a CDB
                        Pdb resource id 4, name pdb1
                        Management policy: AUTOMATIC
                        Start option: OPEN
                        Pdb resource id 5, name pdb2
                        Management policy: AUTOMATIC
                        Start option: OPEN
    Nodes in cluster:
      Node name: host0001, yascs/yasfs inter connect URL: 172.16.1.2:1788, Node ID: 1
        public service port: 1688
        VIP: 192.168.1.62/24/ens192, home node: host0001
      Node name: host0002, yascs/yasfs inter connect URL: 172.16.1.3:1788, Node ID: 2
        public service port: 1688
        VIP: 192.168.1.63/24/ens192, home node: host0002
```

The following describes each configuration item:

| Field Name       |Meaning                  |
| ------------ | -------------------------------- |
| Cluster name | The name of the current cluster. |
| config version | YCR configuration version. The configuration version increments by one each time the cluster registration information is changed after the cluster is created.  |
| Cluster id | The unique ID of the current cluster.|
| Network timeout | The timeout for YCS network heartbeat (in seconds). If this timeout is exceeded, it is considered a network timeout, and YCS enters the fault handling process. <br />This can be modified via the `ycsctl set_ycr` command, with the key `NETWORK_HB_TIMEOUT`. |
| Disk heartbeat keep alive | The timeout for YCS disk heartbeat (in seconds). If this timeout is exceeded, it is considered that the YCS node has experienced a disk heartbeat failure and cannot recover, and YCS enters the fault handling process. <br />This can be modified via the `ycsctl set_ycr` command, with the key `DISK_HB_KEEP_ALIVE`. |
| Fence type | The type of I/O fencing used by the current cluster. <br />This can be modified via the `ycsctl set_ycr` command, with the key `FENCE_TYPE`. |
| Default resource yasfs | The embedded resource YFS, which is enabled by default. |
| Network | Public network configuration for the cluster. |
| Resource SCAN |Whether the cluster opens the SCAN functionality, which is disabled by default.  |
| SCAN name |SCAN domain name.  |
| listening port | Listening port for SCAN.  |
| SCAN VIP | SCAN VIP information, including IP address and ordinal number. |
| Resource vip | Whether the cluster opens the VIP functionality, which is disabled by default.  |
| Database unique name | The unique name of the database in the current cluster.  |
| Database name | The creation name of the database.  |
| Database data directory | The database's DATA directory, i.e., `$YASDB_DATA`. |
| Start option | The startup option for the database — if not explicitly specified during database startup, the database will default to start to this stage. |
| PDBs in database | PDB-related information, applicable only in a CDB. The information items mainly include:  <br/>*   `name`: The name of the PDB.  <br/>*   `Management policy`: The startup strategy of the PDB — whether it follows the database instance's startup.  <br/>*   `Start option`: The startup option for the PDB — if not explicitly specified during PDB startup, the PDB will default to start to this stage. |
| yasdb instance name | The name of the database instance. |
| yasdb instance id | The ID of the database instance. |
| Node name | The name of the node.  |
| yascs/yasfs inter connect URL | The interconnection address between nodes.  |
| Node ID         | The ID of the node.                      |
| public service port | The service port number listened to by the node.  |
| VIP | The configuration information (IP address/subnet mask/network card name) of the VIP resource on the node and its deployment node name (home node). |

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