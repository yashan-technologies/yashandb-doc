### ycsctl add node

This command is used to add an instance node to the cluster during the deployment phase. This command cannot be used after the cluster deployment is complete. 

>**Note**:
>
> During the installation and deployment of YashanDB, the system internally invokes the operation to add an instance node, and no manual execution of this command is required.

This command is used to add a database instance to the cluster during the deployment phase. Append the offline parameter to the command if executing it during the installation and deployment. Do not append this parameter if executing it during the scale-out phase. This command cannot be used after the cluster deployment is complete. The command format is as follows:

```shell
ycsctl add node <nodename> <yascs_url> [--vip <vip_config>] [-p <port>]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| | nodename | Specifies the name for the target instance node, which must match the server name on which the node resides and must be globally unique. The name must consist of 4 to 64 characters, support letters, digits, hyphens (-), and underscores (_), and must start with a letter. |
|          | yascs_url         | Specifies the IP address and port of the instance node's corresponding server, in the format: `IPv4:PORT/[IPv6]:PORT`, for example `192.168.1.2:1770` or `[fc00:7::126]:1688`. The specific specifications for IP addresses can be referenced in [User Guide for *yasql*](../../yasql/User Guide for yasql.md#login). <br />This address is used for internal interconnection among instance nodes in the cluster, and must ensure that the specified IP addresses are reachable and the port numbers do not conflict. |
| --vip    | vip_config     | Optional parameter. Specifies the VIP resource configuration information for the instance  node's corresponding server, in the format: `IP address/subnet mask[/network interface name]`, for example: `192.168.60.4/255.255.255.0/ens192` or `fc00:60::4/64/enp1s0`. For detailed functional descriptions, please refer to [VIP Management](../../../Database Administration/Cluster Management/VIP Management). |
| -p    | port_number | Optional parameter. Specifies the service port number of the instance node's corresponding server. Both the database instance and VIP resources will listen on this port. <br />It must match the port number specified in the [LISTEN_ADDR ](../../../Reference Manual/Configuration Parameters.md#LISTEN_ADDR) parameter configuration of the database instance on this server. |

### ycsctl extend node

This command is used to dynamically extend a new node to the current cluster, or to clean up residual configurations from an incomplete node extension.

The command format and parameter descriptions are as follows:

```shell
ycsctl extend node <-f | --clean> [--post-db]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
| -f | filename | Specifies the absolute path to the configuration file of the new node. |
| --clean | | Cleans up residual configurations from an incomplete node extension. |
| --post-db | | This parameter must be specified if the cluster database has completed instance addition. |

### ycsctl modify node

This command is used to offline adjust the attributes of instance nodes.

The general steps for this operation are as follows:

1. Stop the cluster.
2. Start the yasfs service.
3. Execute this command to modify the corresponding attribute.
4. Stop the [yasfs](../../yasfs) service.
5. Start the cluster.

The command format and parameter descriptions are as follows:

```shell
ycsctl modify node <name> [-nodename <newname>] [-port <newport>] [-url <newurl>]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
|            | nodename | Specifies the target instance node name for executing the current operation, which must be an existing node name in the target database resources. |
| -nodename | newname |  Specifies the new node name, which must be globally unique in the target database resources. |
| -port     | newport | Specifies the new database listening port, which must be an available idle port. |
| -url      | newurl  | Specifies the new YCS listening address in the format `IP:PORT`, which must be an available IP address and idle port. |

### ycsctl start ycs

This command is used to start YCS on the current server and the resources it manages. Whether YashanDB database is started simultaneously with YCS is controlled by the `AUTO_START` parameter.

The command format and parameter descriptions are as follows:

```shell
ycsctl start ycs [-startoption <startoption>] [-p <password>]
```

| Parameter Option   | Values   | Description          |
| ---------- | ----------- | ------------------------------------------------------------ |
|  -startoption<br />-m | NOMOUNT, MOUNT, or OPEN | Optional parameter. Specifies the default startup phase option for the database instance. If omitted, the default value specified by the `ycsctl add database` command will be used.  |
| -p | wallet_password | Optional parameter. Specifies the [wallet password](../../../Product Security/Encryption/Storage Encryption/Key Management) for key management of the database instance. <br />When there are existing encrypted objects in a YAC/Distributed Cluster and you need to start the database directly to MOUNT or OPEN mode, it must be specified; otherwise, the database instance will fail to start.  |

>**Note**:
>
> Please use the `-startoption` option to specify the startup mode. The `-m` option is deprecated and retained only for backward compatibility.

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
> For detailed information on YCS OS Watcher, please refer to [Operating System Load Monitoring](../../../Database Administration/Operation Monitoring/YCS OS Watcher).

The command format is as follows:

```shell
ycsctl stop osw
```

### ycsctl get

This command is used to view the values of [YCS node-level parameters](../../../Database Administration/Cluster Management/Cluster Parameter Configuration.md#Node-Level) on the current server.

The command format and parameter descriptions are as follows:

```shell
ycsctl get <parameter>
```

|Parameter Value | Description          |
| ----------- | ------------------------------------------------------------ |
|  parameter    | Specifies the name of the parameter to be viewed; case-insensitive.  |

### ycsctl set

This command is used to online set the values of [YCS node-level parameters (non-readonly parameters)](../../../Database Administration/Cluster Management/Cluster Parameter Configuration.md#Node-Level) on the current server.

>**Note**:
>
> For the `AUTO_START` parameter, this command only modifies its value in the configuration file; actual effect requires restarting YCS.

The command format and parameter descriptions are as follows:

```shell
ycsctl set <parameter> <value>
```

|Parameter Value | Description          |
| ----------- | ------------------------------------------------------------ |
| parameter    | Specifies the name of the parameter to be modified; case-insensitive.  |
| value         | Specifies a value within the allowable range for the corresponding parameter.     |

### ycsctl show parameter

This command is used to view the configuration of all [YCS node-level parameters](../../../Database Administration/Cluster Management/Cluster Parameter Configuration.md#Node-Level) on the current server.

The command format is as follows:

```shell
ycsctl show parameter
```