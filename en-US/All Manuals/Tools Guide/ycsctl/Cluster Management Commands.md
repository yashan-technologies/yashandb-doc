## ycsctl create cluster

This command is used to create a new cluster. The command format is as follows:

```shell
ycsctl create cluster clustername [-clusterid id] [-o]
```

**clustername**

The name of the cluster, which must be between 4 to 64 characters long and can include letters, numbers, hyphens (-), and underscores (_), and must start with a letter.

**clusterid**

A unique ID for the cluster, which must be 1 to 32 hexadecimal characters long to differentiate the uniqueness of the cluster. This option is optional and a unique ID will be automatically generated when deploying a YAC/Distributed Cluster through *yasboot*.

**-o**

Indicates to overwrite the previous cluster. If this option is added, all configuration information of the previously created cluster will be deleted. **Please use with caution**.

## ycsctl set\_ycr

This command is used to configure parameters for the current cluster. It only supports offline execution and requires stopping the cluster before starting the [yasfs service](../yasfs). After the offline execution is completed, the *yasfs* service must be stopped before restarting the cluster. The command format is as follows:

```shell
ycsctl set_ycr key value
```

**key**

Supports offline modification of NETWORK_HB_TIMEOUT, DISK_HB_KEEP_ALIVE, and FENCE_TYPE. Please stop the cluster before modifying. For detailed parameter descriptions, please refer to [YAC/Distributed Cluster Parameter Configuration](../../Database Administration/Cluster Management/Cluster Parameter Configuration).

**value**

An integer value within the range of the corresponding parameter:

-  NETWORK_HB_TIMEOUT: range [2,600], default value is 30.
- DISK_HB_KEEP_ALIVE: range [2,600], default value is 60.
- FENCE_TYPE: range [0|1], default value is 0.

## ycsctl add node

This command is used to add a node to the cluster during the deployment phase. This command cannot be used after the cluster deployment is complete. The command format is as follows:

```shell
ycsctl add node nodename yascs_url [--vip ip/netmask/interface] [-p port]
```

**nodename**

Assigns a name to the added node, which must be between 4 to 64 characters long and can include letters, numbers, hyphens (-), and underscores (_), and must start with a letter. 

YCS requires the nodename to be consistent with the server name (use the hostnamectl command to view the server's name); otherwise, the cluster will fail to start.

**yascs_url**

The address and port of the server corresponding to the node, formatted as: IPv4:PORT/[IPv6]:PORT, for example: 192.168.1.2:1770 or [fc00:7::126]:1688. Specific specifications for the IP address can be found in the [yasql](../yasql/User Guide for yasql) section.

Multiple YCS nodes are interconnected through yascs_url, and it must be ensured that the specified IP addresses are routable and that there are no port conflicts. 

**--vip**

The VIP resource configuration information for the server corresponding to the node, formatted as: IP address/subnet mask/network card name, for example: 192.168.60.4/255.255.255.0/ens192 or fc00:60::4/64/enp1s0. For specific function descriptions, please refer to [VIP Management](../../Database Administration/Cluster Management/VIP Management).

**-p**

The service port number for the server corresponding to the node, which will be listened to by both the database instance and the VIP resource. It must be consistent with the port number of the configuration parameter [LISTEN_ADDR](../../Reference Manual/Configuration Parameters.html#LISTEN_ADDR) on this server.

## ycsctl add database

This command is used to add a database resource to the cluster during the deployment phase. This command cannot be used after the cluster deployment is complete. The command format is as follows:

```shell
ycsctl add database -db db_unique_name [-dbname] -startshell shellname -stopshell shellname
```

**-db**

The unique name of the database resource in the current cluster, consisting of [1,64] characters composed of letters, numbers, underscores (_), hash signs (#), or dollar signs (\$).  

**-startshell**

The name of the script to start the database. This shell script must be a sh script, with a name length of 4 to 64 characters, and can include letters, numbers, hyphens (-), and underscores (_), and must start with a letter, ending with the .sh suffix.

**-stopshell**

The name of the script to stop the database. This shell script must be a sh script, with a name length of 4 to 64 characters, and can include letters, numbers, hyphens (-), and underscores (_), and must start with a letter, ending with the .sh suffix.

**-dbname**

The creation name of the database. If not specified, the default is to use  the unique name provied by -db.

## ycsctl add instance

This command is used to add a database instance to the cluster during the deployment phase. This command cannot be used after the cluster deployment is complete. The command format is as follows:

```shell
ycsctl add instance -db db_unique_name -node node_name -instance instance_name
```

**-db**

The unique name of the database resource.

**-node**

The name of an existing node in the cluster, on which you are adding the instance.

**-instance**

The name of the adding instance, must be unique across all instances of this database resource.

## ycsctl add pdb

This command is used to add a PDB resource to the YCS in a YAC/Distributed Cluster Deployment CDB. The command format is as follows: 

```shell
ycsctl add pdb -db db_unique_name -pdb pdb_name [-offline]
```

> **Note**:
>
> `-offline` is typically used as an internal command invoked by *yasboot* during YashanDB upgrade. Manual execution by users is **not recommended** unless necessary.

**-db**

The name of the target database for adding PDB should be specified as the unique name of the database in the current cluster.


The current cluster's configuration information can be queried through the ycsctl show config command, where the value of the Database unique name field is this name.  


**-pdb**

Unique name of PDB, must start with a letter, consisting of [1,64] characters composed of letters, numbers, or underscores (_).  


In a YAC/Distributed Cluster Deployment CDB, the process for adding PDBs through *ycsctl* is as follows, and is performed while the CDB is running.

1. Log in to any database installation server as the installation user and execute the ycsctl add pdb command to add a PDB resource to the YCS.

2. Log in to the CDB root and execute the CREATE PLUGGABLE DATABASE statement to add a new PDB, and the PDB name must be consistent.


***Example***

```shell
$ ycsctl add pdb -db yashandb -pdb pdb1
```

## ycsctl remove pdb

This command is used to delete a PDB resource from the YCS in a YAC/Distributed Cluster Deployment CDB. The command format is as follows:  

```shell
ycsctl remove pdb -db db_unique_name -pdb pdb_name
```

You must ensure that the YCS service has already been started before executing this command.


In a YAC/Distributed Cluster Deployment CDB, the process of deleting PDB through *ycsctl* is as follows: 

1. Stop the target PDB, for example, using the ycsctl stop pdb command, directly connecting to the PDB to execute the SHUTDOWN statement, etc.  

2. Execute the DROP PLUGGABLE DATABASE statement on the CDB root to delete the target PDB. 

3. Execute the ycsctl remove pdb command to delete the target PDB resource from the YCS.  


**-db**


The name of the database where the target PDB is located should be specified as the unique name of the database in the current cluster.  

The current cluster's configuration information can be queried through the ycsctl show config command, where the value of the Database unique name field is this name.  


**-pdb**

The name of an existing PDB in the cluster.

***Example***

```shell
$ ycsctl remove pdb -db yashandb -pdb pdb1
```

## ycsctl modify

This command is used to modify the attributes of a cluster after the cluster deployment is complete. It only supports offline execution and requires stopping the cluster before starting the [yasfs service](../yasfs). After the offline execution is completed, the *yasfs* service must be stopped before restarting the cluster. The command format is as follows:

```shell
ycsctl modify object objectname key=value
```

**object**

Management object in the cluster, supports node (cluster node) and instance (database instance).

**objectname**

The name of the object to be modified, such as the name of the cluster node and the name of the database instance. The format for the database instance name is nodename[.instancename], usually one cluster node manages one database instance, so instancename can also be omitted.

**key**

The name of the attribute to be modified, supporting serviceport (node service port number) and instancename (database instance name).

**value**

The value of the attribute to be modified.

***Example***

```shell
# Modify the node service port number
$ ycsctl modify node yas1 serviceport=1601

# Modify the database instance name
$ ycsctl modify instance yas1.yasdb1 instancename=yashandb1
```

## ycsctl import

This command is used to import a specified configuration script into the current cluster. The command format is as follows:

```shell
ycsctl import srcfile [-f]
```

**srcfile**

srcfile is the complete path of the specified configuration script.

**-f**

The imported configuration requires a restart to take effect. Typically, before importing, a node activity check will be performed to prevent online imports from causing configuration inconsistencies. If necessary, this optional parameter can be used to skip the node activity check and force the import. Please use with caution based on actual operational scenarios.

The recommended operational flow is to first stop the entire cluster, then execute the import, and after completion, restart the cluster.

## ycsctl export

This command is used to export configuration script information from the current cluster. The command format is as follows:

```shell
ycsctl export destdir
```

**destdir**

destdir is the target folder to which the configuration will be exported. The exported script is fixed named ycrbackup.sh.

## ycsctl show config

This command is used to view the current cluster configuration information. The command format is as follows:

```shell
ycsctl show config
```

***Example***

```shell
$ ycsctl show config
    Cluster name: yashandb, config version: 6
    Cluster id: 47383c354eda3d04e517cdc21862431e
    Network timeout: 30s
    Disk heartbeat keep alive: 60s
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
                resource id: 0
                Database unique name: yashandb
                Database name: yashandb
                Start shell: start_instance.sh
                Stop shell: stop_instance.sh
                Instances in database:
                        yasdb instance name:yasdb1, yasdb instance id:1
                        yasdb instance name:yasdb2, yasdb instance id:2
    Nodes in cluster:
      Node name: yas1, yascs/yasfs inter connect URL: 172.16.1.2:1788, Node ID: 1
        public service port: 1688
        VIP: 192.168.1.62/24/ens192, home node: yas1
      Node name: yas2, yascs/yasfs inter connect URL: 172.16.1.3:1788, Node ID: 2
        public service port: 1688
        VIP: 192.168.1.63/24/ens192, home node: yas2
```

**Cluster name**

The name of the current cluster.

**config version**

YCR configuration version. The configuration version increments by one each time the cluster registration information is changed after the cluster is created.

**Cluster id**

The unique ID of the current cluster.

**Network timeout**

Specifies the YCS network heartbeat timeout period in seconds. Exceeding this time is considered a network timeout, and YCS enters the fault handling process.

This can be modified using the ycsctl set_ycr command, with the key being NETWORK_HB_TIMEOUT.

**Disk heartbeat keep alive**

YCS disk heartbeat timeout period in seconds. Exceeding this time is considered a disk heartbeat failure for the YCS node, which cannot be recovered, and YCS enters the fault handling process.

This can be modified using the ycsctl set_ycr command, with the key being DISK_HB_KEEP_ALIVE.

**Fence type**

The type of I/O Fencing executed by the YCS.

This can be modified using the ycsctl set_ycr command, with the key being FENCE_TYPE.

**Default resource yasfs**

The embedded resource YFS, which is enabled by default.

**Network**

Public network configuration for the cluster  

**Resource SCAN**

Whether the cluster opens the SCAN functionality, which is disabled by default.

**SCAN name**

SCAN domain name.

**listening port**

Listening port for SCAN.

**SCAN VIP**

SCAN VIP information, including IP address and sequence number.

**Resource vip**

Whether the cluster opens the VIP functionality, which is disabled by default.

**Database unique name**

The unique name of the database in the current cluster.

**Database name**

The creation name of the database.

**Start shell**

The name of the script to start the database instance.

**Stop shell**

The name of the script to stop the database instance.

**yasdb instance id**

The ID of the database instance on the node.

**Node name**

The name of the node in the cluster.

**yascs/yasfs inter connect URL**

The interconnection address between nodes.

**yasdb instance name**

The name of the database instance on the node.

**public service port**

The service port number listened to by the node.

**VIP**

The configuration information (IP address/subnet mask/network card name) of the VIP resource on the node and its deployment node name.

## ycsctl extend node

This command is used to dynamically extend a node or clean up residual data from expansion during cluster operation. If the cluster is in the process of [voting arbitration](../../Product Concepts/YAC Infrastructure/Yashan Cluster Service.html#Voting), executing this command will result in an error, and you need to wait for YCS to complete the voting arbitration before executing again.

> **Caution**:
>
> - For [YAC instance expansion](../../Installation and Upgrade/Scalability/Scalability of YAC Deployment/Scaling of Cluster Instances), it is recommended to use the one-command command provided by the *yasboot* tool, yasboot node add, and **not recommended** to execute this command alone.
>
> - CDBs currently cannot scale YAC instances. 

The command format is as follows:

```shell
ycsctl extend node <-f filename|--clean> [--post-db]
```

**-f**

Path of the configuration file for the new node, supporting absolute and relative paths. It must be an ini file, and configuration items cannot be missing. An example configuration file is as follows:

```shell
$ cat $YASDB_HOME/extend/yas_scale.ini
INTER_ADDR=192.168.1.6:1788      # The interconnection address and port for the new YCS node, formatted as IPv4:PORT/[IPv6]:PORT, ensuring that existing nodes can access this address and that the port is not occupied
NODE_NAME=yas5                   # The name of the new YCS node, which must be globally unique, 4 to 64 characters long, and can include letters, numbers, hyphens (-), and underscores (_), starting with a letter
YASDB_NAME=yasdb                 # The corresponding database instance name for the new YCS node, which must be consistent with the INSTANCE_NAME parameter value in that database
VIP=192.168.60.6/24/ens192       # VIP resource configuration information for the new YCS node, optional. When the cluster has VIP enabled, this must be provided for the new node.
SERVICE_PORT=1601                # Service port number for the new YCS node, optional.
```

**--clean**

Cleans up residual data from unfinished cluster expansion tasks.

**--post-db**

If the cluster database has completed instance addition, this option allows YCS to directly complete the cluster expansion.

## ycsctl add network

This command is used to add public network configuration to the cluster. The command format is as follows:

```shell
ycsctl add network -subnet subnet/netmask[/interface] [-offline] 
```

VIP or SCAN VIP functionality cannot be used until the public network is configured.

**-subnet**  

Public network configuration information, in the format: subnet/subnet_mask/network_interface_name, where the network interface name is optional.

- If no network interface name is specified, any network interface that can access the given subnet will be used.

- If a network interface name is specified, ensure that all servers in the same cluster have a network interface with the same name that can access the given subnet.

**-offline**

Indicates that this command can be executed when YCS is not running.

## ycsctl remove network

This command is used to delete the public network configuration for the cluster. All VIP and SCAN configurations must be deleted before the public network can be deleted.

The command format is as follows:

```shell
ycsctl remove network
```

## ycsctl add vip

This command is used to dynamically add a VIP resource during the cluster operation. The command format is as follows:

```shell
ycsctl add vip -n name --vip ip/netmask[/interface]
```

**-n**

The deployment node name of the VIP resource to be added.

**--vip**

VIP resource configuration information must belong to the public network subnet. Format: IP_address/subnet_mask/network_interface_name, where the network interface name is optional.

- If no network interface name is specified, the network interface name configuration from the network will be inherited. If it is not possible to ensure that all servers in the same cluster have consistent public network interface names, the network interface name must be omitted.

- If a network interface name is specified, ensure it does not conflict with the network specification, and that all servers in the same cluster have a network interface with the same name that can access the public network subnet.

## ycsctl remove vip

This command is used to dynamically remove a VIP resource during the cluster operation. The command format is as follows:

```shell
ycsctl remove vip -n name [-f]
```

**-n**

The deployment node name of the VIP resource to be removed.

**-f**

Indicates to forcibly stop the VIP resource to be removed.

##  ycsctl add scan

This command is used to add a SCAN resource to the cluster. The command format is as follows:


```shell
ycsctl add scan -scanname scan_name -p port [-offline]
```

**-scanname**

SCAN domain name, the name must be a fully qualified hostname.

Client/drivers can connect to the cluster using this name.

**-p**

The listening port for the SCAN.

**-offline**

Indicates that this command can be executed when YCS is not running.

## ycsctl start scan

This command is used to start SCAN VIPs. The command format is as follows:

```shell
ycsctl start scan [-scannumber ordinal_number] [-node node_name]
```

**-scannumber**

The sequence number of the target SCAN VIP (valid values: 1, 2, or 3). The mapping between sequence numbers and SCAN VIPs can be viewed via `ycsctl show config`. If omitted, all SCAN VIPs will be started.

**-node**

Startup node name for SCAN VIP. When omitted, YCS will automatically assign startup nodes according to the principle of even distribution.

## ycsctl stop scan

This command is used to stop SCAN VIPs. The command format is as follows:

```shell
ycsctl stop scan [-scannumber ordinal_number]
```

**-scannumber**

The sequence number of the target SCAN VIP (valid values: 1, 2, or 3). The mapping between sequence numbers and SCAN VIPs can be viewed via `ycsctl show config`. If omitted, all SCAN VIPs will be stopped.

## ycsctl relocate scan

This command is used to manually relocate a SCAN VIP (i.e., manually move a specific SCAN VIP to another node). The command format is as follows:

```shell
ycsctl relocate scan -scannumber ordinal_number [-node node_name]
```

**-scannumber**

The sequence number of the target SCAN VIP (valid values: 1, 2, or 3). The mapping between sequence numbers and SCAN VIPs can be viewed via `ycsctl show config`.

**-node**

Target node. If omitted, YCS will automatically relocate according to high availability and even distribution principles.

## ycsctl remove scan

This command is used to remove a SCAN. The command format is as follows:

```shell
ycsctl remove scan [-f]
```

**-f**

Indicates to forcibly stop the SCAN resource and remove it.
