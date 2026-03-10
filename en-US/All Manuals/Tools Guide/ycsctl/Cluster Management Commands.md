### ycsctl create cluster

This command is used to create a new cluster. The command format is as follows:

```shell
ycsctl create cluster clustername [-clusterid id] [-o]
```

**clustername**

The name of the cluster, which must be between 4 to 64 characters long and can include letters, numbers, hyphens (-), and underscores (_), and must start with a letter.

**clusterid**

A unique ID for the cluster, which must be 1 to 32 hexadecimal characters long to differentiate the uniqueness of the cluster. This option is optional and a unique ID will be automatically generated when deploying a YAC through *yasboot*.

**-o**

Indicates to overwrite the previous cluster. If this option is added, all configuration information of the previously created cluster will be deleted. **Please use with caution**.

### ycsctl set\_ycr

This command is used to configure parameters for the current cluster. It only supports offline execution and requires stopping the cluster before starting the [yasfs service](../yasfs). After the offline execution is completed, the *yasfs* service must be stopped before restarting the cluster. The command format is as follows:

```shell
ycsctl set_ycr key value
```

**key**

Supports offline modification of NETWORK_HB_TIMEOUT, DISK_HB_KEEP_ALIVE, and FENCE_TYPE. Please stop the cluster before modifying. For detailed parameter descriptions, please refer to [YAC Configuration](../../YashanDB for Cluster/Yashan Cluster Service/YAC Configuration).

**value**

An integer value within the range of the corresponding parameter:

-  NETWORK_HB_TIMEOUT: range [2,600], default value is 30.
- DISK_HB_KEEP_ALIVE: range [2,600], default value is 60.
- FENCE_TYPE: range [0|1], default value is 0.

### ycsctl add node

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

The VIP resource configuration information for the server corresponding to the node, formatted as: IP address/subnet mask/network card name, for example: 192.168.60.4/255.255.255.0/ens192 or fc00:60::4/64/enp1s0. For specific function descriptions, please refer to [VIP Management](../../共享集群/集群服务管理/VIP管理).

**-p**

The service port number for the server corresponding to the node, which will be listened to by both the database instance and the VIP resource. It must be consistent with the port number of the configuration parameter [LISTEN_ADDR](../../Reference Manual/Configuration Parameters.html#LISTEN_ADDR) on this server.

### ycsctl add yasdbinstance

This command is used to add a database instance to the cluster during the deployment phase. This command cannot be used after the cluster deployment is complete. The command format is as follows:

```shell
ycsctl add yasdbinstance nodename.yasdbinstancename startshell stopshell monitorshell
```

**nodename**

The name of an existing node in the cluster.

**yasdbinstancename**

The name of the database instance installed on the node, which must be consistent with the INSTANCE_NAME parameter value in that database and cannot be duplicated.

**startshell**

The name of the script to start the database instance. The start script names must be consistent across all nodes in the cluster.

This shell script must be a sh script, with a name length of 4 to 64 characters, and can include letters, numbers, hyphens (-), and underscores (_), and must start with a letter, ending with the .sh suffix.

**stopshell**

The name of the script to stop the database instance. The stop script names must be consistent across all nodes in the cluster.

This shell script must be a sh script, with a name length of 4 to 64 characters, and can include letters, numbers, hyphens (-), and underscores (_), and must start with a letter, ending with the .sh suffix.

**monitorshell**

The name of the script for monitoring the database instance. The monitoring script is a reserved functionality that only needs to be configured here. 

it is not required for this script to exist.

### ycsctl modify

This command is used to modify the attributes of a cluster after the cluster deployment is complete. It only supports offline execution and requires stopping the cluster before starting the [yasfs service](../yasfs). After the offline execution is completed, the *yasfs* service must be stopped before restarting the cluster. The command format is as follows:

```shell
ycsctl modify object objectname key=value
```

**object**

Management object in the cluster, supports node (cluster node) and yasdbinstance (database instance).

**objectname**

The name of the object to be modified, such as the name of the cluster node and the name of the database instance. The format for the database instance name is nodename[.yasdbinstancename], usually one cluster node manages one database instance, so yasdbinstancename can also be omitted.

**key**

The name of the attribute to be modified, supporting serviceport (node service port number) and yasdbinstancename (database instance name).

**value**

The value of the attribute to be modified.

***Example***

```shell
# Modify the node service port number
$ ycsctl modify node yas1 serviceport=1601

# Modify the database instance name
$ ycsctl modify yasdbinstance yas1.yasdb1 yasdbinstancename=yashandb1
```

### ycsctl import

This command is used to import a specified configuration script into the current cluster. The command format is as follows:

```shell
ycsctl import srcfile [-f]
```

**srcfile**

srcfile is the complete path of the specified configuration script.

**-f**

The imported configuration requires a restart to take effect. Typically, before importing, a node activity check will be performed to prevent online imports from causing configuration inconsistencies. If necessary, this optional parameter can be used to skip the node activity check and force the import. Please use with caution based on actual operational scenarios.

The recommended operational flow is to first stop the entire cluster, then execute the import, and after completion, restart the cluster.

### ycsctl export

This command is used to export configuration script information from the current cluster. The command format is as follows:

```shell
ycsctl export destdir
```

**destdir**

destdir is the target folder to which the configuration will be exported. The exported script is fixed named ycrbackup.sh.

### ycsctl show config

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
    Network: 192.168.1.0/24
    Resource SCAN: enabled
    SCAN name: scan.example.com, listening port: 1688
      SCAN VIP: 192.168.1.100, ordinal number: 1
      SCAN VIP: 192.168.1.101, ordinal number: 2
      SCAN VIP: 192.168.1.102, ordinal number: 3
    Resource vip: enabled
    Shell in cluster:
      Start shell:   start.sh
      Stop shell:    stop.sh
      Monitor shell: monitor.sh
    Nodes in cluster:
      Node name: yas1, yascs/yasfs inter connect URL: 172.16.1.2:1788, Node ID: 1
        public service port: 1688
        yasdb instance name:yasdb-1-1, yasdb instance id:1
        VIP: 192.168.1.62/24/ens192, home node: yas1
      Node name: yas2, yascs/yasfs inter connect URL: 172.16.1.3:1788, Node ID: 2
        public service port: 1688
        yasdb instance name:yasdb-1-2, yasdb instance id:1
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

**Start shell**

The name of the script to start the database instance.

**Stop shell**

The name of the script to stop the database instance.

**Monitor shell**

The name of the script to monitor the database instance.

**Node name**

The name of the node in the cluster.

**yascs/yasfs inter connect URL**

The interconnection address between nodes.

**yasdb instance name**

The name of the database instance on the node.

**public service port**

The service port number listened to by the node.

**yasdb instance id**

The ID of the database instance on the node.

**VIP**

The configuration information (IP address/subnet mask/network card name) of the VIP resource on the node and its deployment node name.

### ycsctl extend node

This command is used to dynamically extend a node or clean up residual data from expansion during cluster operation. If the cluster is in the process of [voting arbitration](../../YashanDB for Cluster/High Availability of YAC/High Availability of YCS), executing this command will result in an error, and you need to wait for YCS to complete the voting arbitration before executing again.

> **Caution**:
>
> For [YAC instance expansion](../../Installation and Upgrade/Scalability/Scalability of YAC Deployment/Scaling of Cluster Instances), it is recommended to use the one-command command provided by the *yasboot* tool, yasboot node add, and **not recommended** to execute this command alone.

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

### ycsctl add network

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

### ycsctl remove network

This command is used to delete the public network configuration for the cluster. All VIP and SCAN configurations must be deleted before the public network can be deleted.

The command format is as follows:

```shell
ycsctl remove network
```

### ycsctl add vip

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

### ycsctl remove vip

This command is used to dynamically remove a VIP resource during the cluster operation. The command format is as follows:

```shell
ycsctl remove vip -n name [-f]
```

**-n**

The deployment node name of the VIP resource to be removed.

**-f**

Indicates to forcibly stop the VIP resource to be removed.

###  ycsctl add scan

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

### ycsctl start scan

This command is used to start SCAN VIPs. The command format is as follows:

```shell
ycsctl start scan [-scannumber ordinal_number] [-node node_name]
```

**-scannumber**

The sequence number of the target SCAN VIP (valid values: 1, 2, or 3). The mapping between sequence numbers and SCAN VIPs can be viewed via `ycsctl show config`. If omitted, all SCAN VIPs will be started.

**-node**

Startup node name for SCAN VIP. When omitted, YCS will automatically assign startup nodes according to the principle of even distribution.

### ycsctl stop scan

This command is used to stop SCAN VIPs. The command format is as follows:

```shell
ycsctl stop scan [-scannumber ordinal_number]
```

**-scannumber**

The sequence number of the target SCAN VIP (valid values: 1, 2, or 3). The mapping between sequence numbers and SCAN VIPs can be viewed via `ycsctl show config`. If omitted, all SCAN VIPs will be stopped.

### ycsctl relocate scan

This command is used to manually relocate a SCAN VIP (i.e., manually move a specific SCAN VIP to another node). The command format is as follows:

```shell
ycsctl relocate scan -scannumber ordinal_number [-node node_name]
```

**-scannumber**

The sequence number of the target SCAN VIP (valid values: 1, 2, or 3). The mapping between sequence numbers and SCAN VIPs can be viewed via `ycsctl show config`.

**-node**

Target node. If omitted, YCS will automatically relocate according to high availability and even distribution principles.

### ycsctl remove scan

This command is used to remove a SCAN. The command format is as follows:

```shell
ycsctl remove scan [-f]
```

**-f**

Indicates to forcibly stop the SCAN resource and remove it.