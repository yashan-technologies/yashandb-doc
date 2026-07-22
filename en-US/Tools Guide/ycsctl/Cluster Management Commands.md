### ycsctl create cluster

This command is used to create a new cluster. 

The command format and parameter descriptions are as follows:

```shell
ycsctl create cluster clustername [-clusterid id] [-o]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
|            | clustername |  Specifies the cluster name, consisting of 4 to 64 characters. Supports letters, digits, hyphens (-), and underscores (_), and must start with a letter.  |
| -clusterid | ID          | Optional parameter. Specifies the unique ID of the cluster, consisting of 1 to 32 hexadecimal characters, used to distinguish the cluster's uniqueness. |
| -o         |         | Optional parameter. Indicates that existing cluster configuration information will be overwritten.<br />Specifying this option will overwrite existing cluster configuration information and cannot be rolled back. Unless absolutely necessary, **DO NOT** specify this option. |

### ycsctl set_ycr

This command is used to offline adjust the values of YCS cluster-level parameters in the current cluster.



The general steps for this operation are as follows:

2. Stop the cluster.
4. Start the yasfs service.
6. Execute this command to modify the value of the corresponding parameter.
8. Stop the [yasfs](../yasfs) service.
10. Start the cluster.



The command format and parameter descriptions are as follows:

```shell
ycsctl set_ycr key value
```

|Parameter Value | Description          |
| ----------- | ------------------------------------------------------------ |
| key         | Specifies the name of the parameter to be modified; case-insensitive.  |
| value         | Specifies a value within the allowable range for the corresponding parameter.     |


### ycsctl add node

This command is used to add an instance node to the cluster during the deployment phase. This command cannot be used after the cluster deployment is complete. 

>**Note**:
>
> During the installation and deployment of YashanDB, the system internally invokes the operation to add an instance node, and no manual execution of this command is required.

This command is used to add a database instance to the cluster during the deployment phase. Append the offline parameter to the command if executing it during the installation and deployment. Do not append this parameter if executing it during the scale-out phase. This command cannot be used after the cluster deployment is complete. The command format is as follows:

```shell
ycsctl add node nodename yascs_url [--vip ip/netmask[/interface]] [-p port]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| | nodename | Specifies the name for the target instance node, which must match the server name on which the node resides and must be globally unique. The name must consist of 4 to 64 characters, support letters, digits, hyphens (-), and underscores (_), and must start with a letter. |
|          | yascs_url         | Specifies the IP address and port of the instance node's corresponding server, in the format: `IPv4:PORT/[IPv6]:PORT`, for example `192.168.1.2:1770` or `[fc00:7::126]:1688`. The specific specifications for IP addresses can be referenced in [User Guide for *yasql*](../yasql/User Guide for yasql.md#login). <br />This address is used for internal interconnection among instance nodes in the cluster, and must ensure that the specified IP addresses are reachable and the port numbers do not conflict. |
| --vip    | IP/netmask/interface        | Optional parameter. Specifies the VIP resource configuration information for the instance  node's corresponding server, in the format: `IP address/subnet mask[/network interface name]`, for example: `192.168.60.4/255.255.255.0/ens192` or `fc00:60::4/64/enp1s0`. For detailed functional descriptions, please refer to [VIP Management](../../Database Administration/Cluster Management/VIP Management). |
| -p    | port_number | Optional parameter. Specifies the service port number of the instance node's corresponding server. Both the database instance and VIP resources will listen on this port. <br />It must match the port number specified in the [LISTEN_ADDR ](../../Reference Manual/Configuration Parameters.md#LISTEN_ADDR) parameter configuration of the database instance on this server. |

### ycsctl add database

This command is used to add a database resource to the cluster during the deployment phase. This command cannot be used after the cluster deployment is complete. 

>**Note**:
>
> During the installation and deployment of YashanDB, the system internally invokes the operation to add a database, and no manual execution of this command is required.

The command format and parameter descriptions are as follows:

```shell
ycsctl add database -db db_unique_name [-dbname db_name] [-startoption start_options]
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
ycsctl add instance -db db_unique_name -node nodename -instance instancename -datahome db_data_home
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -db | db_unique_name | Specifies the unique name of the target database resource for the current operation, which must be an existing database resource.<br />This name can be queried via the `ycsctl show config` command, where the value of the `Database unique name` field represents this name.  |
| -node | nodename | Specifies the name of the target instance node for the current operation, which must be an existing node name within the target database resource. |
| -instance | instancename | Specifies the name for the newly added database instance; it must be globally unique within the target database resource. |
| -datahome | db_data_home | Specifies the $YASDB_DATA directory for the database; an absolute path must be specified, for example, `/data/yashan/yasdb_data`. |

<span id="addpdb" name="addpdb"></span>

### ycsctl add pdb

This command is used to add a PDB resource to the YCS in a YAC/Distributed Cluster Deployment CDB. 

> **Note**:
>
> `-offline` is typically used as an internal command invoked by *yasboot* during YashanDB upgrade. Manual execution by users is **not recommended** unless necessary.

The command format and parameter descriptions are as follows: 

```shell
ycsctl add pdb -db db_unique_name -pdb pdb_name [-policy policy] [-startoption start_options] [-offline]
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



### ycsctl remove pdb

This command is used to delete a PDB resource from the YCS in a YAC/Distributed Cluster Deployment CDB. 

The command format and parameter descriptions are as follows:  

```shell
ycsctl remove pdb -db db_unique_name -pdb pdb_name
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -db | db_unique_name | Specifies the unique name of the target database resource for the current operation, which must be an existing database resource.<br />This name can be queried via the `ycsctl show config` command, where the value of the `Database unique name` field represents this name.    |
| -pdb | pdb_name | Specifies the name of the PDB resource to be removed, which must be an existing PDB name within the target database resource. |

You must ensure that the YCS service has already been started before executing this command.


In a YAC/Distributed Cluster Deployment CDB, the process of deleting PDB through *ycsctl* is as follows: 

1. Stop the target PDB, for example, using the ycsctl stop pdb command, directly connecting to the PDB to execute the SHUTDOWN statement, etc.  

2. Execute the DROP PLUGGABLE DATABASE statement on the CDB root to delete the target PDB. 

3. Execute the ycsctl remove pdb command to delete the target PDB resource from the YCS.  


### ycsctl extend node

This command is used to dynamically extend a new node to the current cluster, or to clean up residual configurations from an incomplete node extension.

The command format and parameter descriptions are as follows:

```shell
ycsctl extend node <-f filename|--clean> [--post-db]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -f | filename | Specifies the absolute path to the configuration file of the new node. |
| --clean | | Cleans up residual configurations from an incomplete node extension. |
| --post-db | | This parameter must be specified if the cluster database has completed instance addition. |

This command is used to offline adjust the attributes of instance nodes or database instances in the cluster. Currently, it only supports modifying the following parameters:

- Instance node service port, parameter name: `serviceport`
- Database instance name, parameter name: `instancename`

The command format and parameter descriptions are as follows:

```shell
ycsctl modify object objectname key=value
```

| Values   | Description          |
| ----------- | ------------------------------------------------------------ |
| node or instance | Specifies the type of object to be operated on.  <br/>*   node: To operate on an instance node  <br/>*   instance: To operate on a database instance |
| objectname | Specifies the name of the object to be operated on. <br />When specifying the database instance name, the format is: `node_name.instance_name` |
| key=value | Specifies the key-value pair to be modified.  <br/>*   To modify the instance node service port, format: `serviceport=port_number`. A complete command example is: `ycsctl modify node host0001 serviceport=1601`  <br/>*   To modify the database instance name, format: `instancename=instance_name`. A complete command example is: `ycsctl modify instance host0001.yasdb1 instancename=yashandb1` |



The general steps for this operation are as follows:

2. Stop the cluster.
4. Start the yasfs service.
6. Execute this command to modify the corresponding attribute.
8. Stop the [yasfs](../yasfs) service.
10. Start the cluster.



### ycsctl import

This command is used to import a specified configuration script into the current cluster. 

The recommended operational flow is to first stop the entire cluster, then execute the import, and after completion, restart the cluster.

The command format and parameter descriptions are as follows:

```shell
ycsctl import srcfile [-f]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
|  | srcfile | Specifies the full path of the configuration script to be imported. |
| -f | | Optional parameter. Indicates forced import. <br />By default, the system will perform node liveness checks before importing to avoid configuration inconsistencies during online import. <br />Specifying this option will skip the node liveness check; use it with caution based on your actual operational scenario. |

### ycsctl export

This command is used to export configuration script information for the current cluster. After export, the file name is fixed as `ycrbackup.sh`.

The command format and parameter descriptions are as follows:

```shell
ycsctl export destdir
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

### ycsctl add network

This command is used to add public network configuration to the cluster.  VIP or SCAN VIP functionality cannot be used until the public network is configured.

The command format and parameter descriptions are as follows:

```shell
ycsctl add network -subnet subnet/netmask[/interface] [-offline] 
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -subnet | subnet/netmask[/interface] | Specifies the public network configuration information, in the format: `subnet/subnet mask/network interface name`, where the network interface name is optional.<br />* If no network interface name is specified, any network interface that can access the given subnet will be used. <br />* If a network interface name is specified, ensure that all servers in the same cluster have a network interface with the same name that can access the given subnet. |
| -offline | | Optional parameter. Indicates that this command can be executed even when YCS is not running. |

### ycsctl remove network

This command is used to delete the public network configuration for the cluster. All VIP and SCAN configurations must be deleted before the public network can be deleted.

The command format is as follows:

```shell
ycsctl remove network
```

### ycsctl add vip

This command is used to dynamically add a VIP resource during the cluster operation. 

The command format and parameter descriptions are as follows:

```shell
ycsctl add vip -n node_name --vip ip/netmask[/interface]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -n | node_name | Specifies the name of the instance node to which the VIP resource should be added. |
| --vip | ip/netmask[/interface] | Specifies the VIP resource configuration information, which must belong to a public subnet. The format is: `IP address/subnet mask/network interface name`, where the network interface name is optional.<br />* If no network interface name is specified, the network interface name configuration from the network will be inherited. If it is not possible to ensure that all servers in the same cluster have consistent public network interface names, the network interface name must be omitted.<br />* If a network interface name is specified, ensure it does not conflict with the network specification, and that all servers in the same cluster have a network interface with the same name that can access the public network subnet. |

### ycsctl remove vip

This command is used to dynamically remove a VIP resource during the cluster operation. 

The command format and parameter descriptions are as follows:

```shell
ycsctl remove vip -n node_name [-f]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -n | node_name | Specifies the name of the instance node where the target VIP resource resides. This name can be queried via the `ycsctl show config` command, where the value of the `home node` field for the target VIP resource represents this name. |
| -f | | Optional parameter. Indicates to forcibly stop the target VIP resource to be removed. |

### ycsctl add scan

This command is used to add a SCAN resource to the cluster. 

The command format and parameter descriptions are as follows:

```shell
ycsctl add scan -scanname scan_domain_name -p port_number [-offline]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -scanname | scan_domain_name | Specifies the SCAN domain name, which must be a fully qualified hostname. <br />Clients/drivers can connect to the database via this name. |
| -p | port_number | Specifies the listening port for SCAN. |
| -offline | | Optional parameter. Indicates that this command can be executed even when YCS is not running. |

### ycsctl start scan

This command is used to start SCAN VIPs. 

The command format and parameter descriptions are as follows:

```shell
ycsctl start scan [-scannumber ordinal_number] [-node node_name]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -scannumber | ordinal_number | Optional parameter. Specifies the ordinal number of the target SCAN VIP to be started, with allowed values of 1, 2, or 3. If omitted, it indicates starting all SCAN VIPs. <br />The mapping between ordinal numbers and SCAN VIPs can be viewed via `ycsctl show config`. |
| -node | node_name | Optional parameter. Specifies the name of the instance node for the target SCAN VIP to be started. If omitted, YCS will automatically assign the starting node in a round-robin fashion. |

### ycsctl stop scan

This command is used to stop SCAN VIPs. 

The command format and parameter descriptions are as follows:

```shell
ycsctl stop scan [-scannumber ordinal_number]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -scannumber | ordinal_number | Optional parameter. Specifies the ordinal number of the target SCAN VIP to be stopped, with allowed values of 1, 2, or 3. If omitted, it indicates starting all SCAN VIPs. <br />The mapping between ordinal numbers and SCAN VIPs can be viewed via `ycsctl show config`. |

### ycsctl relocate scan

This command is used to manually relocate a SCAN VIP (i.e., manually move a specific SCAN VIP to another node). 

The command format and parameter descriptions are as follows:

```shell
ycsctl relocate scan -scannumber ordinal_number [-node node_name]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -scannumber | ordinal_number | Optional parameter. Specifies the ordinal number of the target SCAN VIP to be relocated, with allowed values of 1, 2, or 3. If omitted, it indicates starting all SCAN VIPs. <br />The mapping between ordinal numbers and SCAN VIPs can be viewed via `ycsctl show config`. |
| -node | node_name | Optional parameter. Specifies the name of the target node to which the target SCAN VIP will be relocated. If omitted, YCS will automatically relocate the SCAN VIP according to high availability and round-robin principles. |

### ycsctl remove scan

This command is used to remove a SCAN. 

The command format and parameter descriptions are as follows:

```shell
ycsctl remove scan [-f]
```

| Parameter Option   | Description          |
| ---------- | ------------------------------------------------------------ |
| -f | Optional parameter. Indicates forced termination and deletion of the SCAN. |
