## Functionality Overview

The yacConnect function is used to initiate a connection request from the client. It returns YAC_SUCCESS to indicate a successful connection and YAC_ERROR to indicate a connection failure.

## Function Declaration

```c
yacResult yacConnect ( yacHandle hConn,
                       const yacChar* url,
                      YacInt16 urlLength,
                      const yacChar* user,
                      YacInt16 userLength,
                      const yacChar* password,
                      YacInt16 passwordLength);
```

## Parameter Description

|Parameter Name |Description |
| ------------------- |---------------------------------------------------------|
| hConn (IN/OUT)      | Connection information handle.                  |
| url (IN)            | The URL of the target database. It supports connection via single address, multiple addresses, or multiple address groups. For detailed introduction, please refer to the following content. |
| urlLength (IN)      | Length of the server address; you can pass YAC_NULL_TERM_STR to indicate a null-terminated string.                     |
| user (IN)           | Username.                                  |
| userLength (IN)     | Length of the username; you can pass YAC_NULL_TERM_STR to indicate a null-terminated string.             |
| password (IN)       | User password.              |
| passwordLength (IN) | Length of the user password; you can pass YAC_NULL_TERM_STR to indicate a null-terminated string.           |

URL format is as follows:

* Single address connection: `host:port[/pdb_name]`.

* Multiple addresses connection: `serverType:host:port,host:port,host:port,host:port[/pdb_name]`, multiple addresses are separated by `,`, and the connection is made to the corresponding node based on the serverType configuration during connection.

* Multiple address groups connection: `serverType:host:port,host:port;host:port,host:port[/pdb_name]`, multiple groups of addresses are separated by `;` and multiple addresses within the same group are separated by `,`. During connection, the connection is first made to the corresponding node within the group based on the serverType configuration. When all connections within the group fail, the next group is accessed in order of priority (the earlier, the higher priority).

Parameter meanings:

* host:port[/pdb_name]：

    * host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name. In YAC deployment, if [SCAN](../../../../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../../../../Database Administration/Cluster Management/VIP Management) has been configured, it can also be the corresponding domain name or IP address.

    * port: The listening port of the YashanDB server. If not adjusted during installation, the default is 1688. 
    * pdb_name: Only applicable to CDBs. Specifies connecting to a specific PDB. If omitted, defaults to connecting to the CDB root.

* serverType: The connection type for multi-address connection. Optional values include primary, standby, loadBalance, primaryLoadBalance, and standbyLoadBalance. If serverType is not specified, primary is used by default when multiple IPs are entered. The detailed introduction of each type is as follows:

    |serverType |Description |
    |--------------------|---------------|
    | primary | This is the default type and can be omitted.<br />The driver will connect to the nodes in the order of the specified listening addresses, determine the node roles by executing `SELECT * FROM DATABASE_ROLE`, and retain the connection established with the primary node for the first time. |
    | standby | The driver will connect to the nodes in the order of the specified listening addresses, determine the node roles by executing `SELECT * FROM DATABASE_ROLE`, and retain the connection established with the standby node for the first time.  |
    | loadBalance | The driver will randomly shuffle the specified listening addresses and then establish connections. It will obtain the current session count for each node and the node with the minimum session count as the target node (if there are multiple nodes with the same minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections.  |
    | primaryLoadBalance | The driver randomly shuffles the specified listening addresses and then attempts connections. It will obtain the current session count and role of each node and select the node with the minimum session count among primary nodes as the target node (if there are multiple nodes with the same  minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections.  |
    | standbyLoadBalance | The driver randomly shuffles the specified listening addresses and then attempts connections. It will obtain the current session count and role of each node and select the node with the minimum session count among standby nodes as the target node (if there are multiple nodes with the same  minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections.  |





>**Note**:
>
> - The failover efficiency of multiple groups of listener addresses is lower than that of multiple listener addresses. Grouping is mainly used to ensure that database connections are established as much as possible only through the first group of addresses. Please choose whether to group based on actual needs.
> - In YAC/Distributed Cluster Deployment, if [SCAN](../../../../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../../../../Database Administration/Cluster Management/VIP Management) has been configured, you can directly use the HA capabilities provided by the database server, and there is **no need** to configure an additional multi-address connection.
> - When configuring multiple addresses to connect to a YAC or a distributed cluster:
>   - In the case of single-cluster deployment, the driver will regard all its instances as primary nodes.
>   - In the case of primary-standby cluster deployment, the driver regards all instances in the primary cluster as primary nodes and all instances in the standby cluster as standby nodes. If load balancing is required in scenarios where SCAN or VIP is not configured, you can consider configuring multiple groups of address connections and specifying primaryLoadBalance or standbyLoadBalance as needed. All instances in the primary cluster form one group, and all instances in the standby cluster form another group.



