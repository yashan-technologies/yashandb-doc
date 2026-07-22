<span id="IPs" name="IPs"></span>

## Multi-Address Connection Configuration

When connecting to YashanDB with multiple nodes using the driver, you can configure multiple listening addresses (`host:port`) or multiple groups of such addresses for connection.

- Multiple listening addresses are separated by `,`, and the connection is made to the corresponding node based on the service type (serverType) configuration during connection.

- Multiple groups of listening addresses are separated by `;` and multiple listening addresses within the same group are separated by `,`. During connection, the connection is first made to the corresponding node within the group based on the serverType configuration. When all connections within the group fail, the next group is accessed in order of priority (the earlier, the higher priority).



>**Note**:
>
> - The failover efficiency of multiple groups of listener addresses is lower than that of multiple listener addresses. Grouping is mainly used to ensure that database connections are established as much as possible only through the first group of addresses. Please choose whether to group based on actual needs.
> - In YAC/Distributed Cluster Deployment, if [SCAN](../../../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../../../Database Administration/Cluster Management/VIP Management) has been configured, you can directly use the HA capabilities provided by the database server, and there is **no need** to configure an additional multi-address connection.
> - When configuring multiple addresses to connect to a YAC or a distributed cluster:
>   - In the case of single-cluster deployment, the driver will regard all its instances as primary nodes.
>   - In the primary-standby cluster deployment, the driver regards all instances in the primary cluster as primary nodes and all instances in the standby cluster as standby nodes. If load balancing is required in scenarios where SCAN or VIP is not configured, you can consider configuring multiple groups of address connections and specifying primaryLoadBalance or standbyLoadBalance as needed. All instances in the primary cluster form one group, and all instances in the standby cluster form another group.




URL parameter format:

```java
jdbc:yasdb:serverType://host1:port1,host2:port2;host3:port3,host4:port4/databasename

// When serverType=primary|standby, poolTimeout can be specified
jdbc:yasdb:serverType://host1:port1,host2:port2;host3:port3,host4:port4/databasename?poolTimeout=value
```

Introduction to configuration items is as follows:

- serverType: The connection type for multi-address connection. Optional values include primary, standby, loadBalance, primaryLoadBalance, and standbyLoadBalance. If serverType is not specified, primary is used by default when multiple IPs are entered. The detailed introduction of each type is as follows:

    |serverType |Description |
    |--------------------|---------------|
    | primary | This is the default type and can be omitted.<br />The driver will connect to the nodes in the order of the specified listening addresses, determine the node roles by executing `SELECT * FROM DATABASE_ROLE`, and retain the connection established with the primary node for the first time. |
    | standby | The driver will connect to the nodes in the order of the specified listening addresses, determine the node roles by executing `SELECT * FROM DATABASE_ROLE`, and retain the connection established with the standby node for the first time.  |
    | loadBalance | The driver will randomly shuffle the specified listening addresses and then establish connections. It will obtain the current session count for each node and the node with the minimum session count as the target node (if there are multiple nodes with the same minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections.  |
    | primaryLoadBalance | The driver randomly shuffles the specified listening addresses and then attempts connections. It will obtain the current session count and role of each node and select the node with the minimum session count among primary nodes as the target node (if there are multiple nodes with the same  minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections.  |
    | standbyLoadBalance | The driver randomly shuffles the specified listening addresses and then attempts connections. It will obtain the current session count and role of each node and select the node with the minimum session count among standby nodes as the target node (if there are multiple nodes with the same  minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections.  |



- poolTimeout: This parameter can only be used with the "primary" or "standby" types and represents the connection timeout in seconds. It is an optional parameter, and if omitted, the default value is 300. During the entire connection process, even if all addresses are quickly traversed and none of them result in a successful connection, the system will still wait for the duration of poolTimeout before prompting a connection failure. 

URL connection configuration examples:

```java
// Example 1: primary + multiple IP/PORT + poolTimeout
jdbc:yasdb:primary://192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688/yashan?poolTimeout=180

// Example 2: standby + multiple sets of IP/PORT + poolTimeout
jdbc:yasdb:standby://192.168.1.2:1688,192.168.1.3:1688;192.168.1.4:1688,1688,192.168.1.5:1688/yashan?poolTimeout=180

// Example 3: loadBalance + multiple IP/PORT 
jdbc:yasdb:loadBalance://192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688/yashan

// Example 4: primaryLoadBalance + multiple sets of IP/PORT
jdbc:yasdb:primaryLoadBalance://192.168.1.2:1688,192.168.1.3:1688;192.168.1.4:1688,1688,192.168.1.5:1688/yashan

// Example 5: standbyLoadBalance + multiple IP/PORT
jdbc:yasdb:standbyLoadBalance://192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688/yashan
```

## Transparent Application Failover (TAF) Configuration

The following URL parameters related to the TAF functionality exist in YashanDB JDBC:

|Parameter Name |Data Type |Parameter Description |
| ----------------- | --------------------- |--------------------------------------------|
| failover            | string    | Whether to enable TAF. The value can be ON or OFF, default is OFF.<br />* ON: Indicates TAF is enabled, and the application will automatically switch to the other configured database nodes in case of a failure.<br />* OFF: Indicates TAF is disabled, and automatic reconnection to the current database node is not supported in OFF state. |
| failoverType        | string    | Type of Failover, supports NONE, SESSION, and SELECT, default is NONE.<br />* NONE: Indicates no Failover is used.<br />* SESSION: Indicates reconnecting and resetting the Statement handle after a failure.<br />* SELECT: Indicates re-executing the failed SELECT statement (the SELECT statement cannot be a query with LOB-type binding parameters) after switching the connection, but it does not support secondary operations on previously obtained objects.                      |
| failoverMethod      | string    | The method of Failover, which will determine the speed of Failover from the primary node to the standby node.<br/>* BASIC: Indicates that a new connection is established during Failover. |
| failoverRetries     | int       | Number of retry attempts, default value is 5.                                |
| failoverDelay       | int       | Time interval for retries (in seconds), default value is 1.                    |

The format for URL parameters is:

```java
jdbc:yasdb:serverType://host1:port1,host2:port2;host3:port3,host4:port4/yashan?key1=value1&key2=value2
```

Example of URL connection configuration:

```java
jdbc:yasdb:primary://192.168.1.2:1688,192.168.1.3:1688;192.168.1.4:1688,192.168.1.5:1688;192.168.1.6:1688,192.168.1.7:1688/yashan?poolTimeout=180&failover=on&failoverType=session&failoverMethod=basic&failoverRetries=5&failoverDelay=1
```

When configuring [multiple groups of IP/PORT connections](#IPs), connections are made within a group according to the serverType, and between groups based on priority order. For example, if there are three groups of IP/PORT connections and a TAF event occurs on a node in the second group, it will first try other nodes in the second group, then access nodes in the first group, and finally nodes in the third group.

## Heartbeat Connection Configuration

Enabling heartbeat connections allows the program to detect network anomalies faster on active connections, triggering subsequent operations such as fault recovery.

Heartbeat-related configuration parameters are as follows:

|Parameter |Type |Description |
| ------------------------- | ------------------------------------------------------------ |---------------------------------------------------|
| heartbeatSwitch             | string | This parameter indicates whether heartbeat connections are enabled, with ON and OFF as options, default is ON. |
| heartbeatSocketTimeout      | int    | Heartbeat connection keep-alive timeout (in seconds), default is 60 seconds. |
| heartbeatSchedulePeriod     | int    | Heartbeat connection keep-alive interval (in seconds), default is 20 seconds. |

URL connection configuration example:

```java
jdbc:yasdb://192.168.1.2:1688/yashan?heartbeatSwitch=on&heartbeatSocketTimeout=60&heartbeatSchedulePeriod=20
```
