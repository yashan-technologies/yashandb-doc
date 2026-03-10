<span id="IPs" name="IPs" class="yaslink"></span>

## Multi-IP/PORT Connection Configuration

In high availability primary/standby and load balancing scenarios, multiple IP/PORT or multiple sets of IP/PORT can be configured for connection.

Multiple IP/PORT should be separated by commas, and connections will be made to the corresponding nodes based on the serverType configuration.

Multiple sets of IP/PORT should be separated by semicolons; connections are first made based on serverType configuration for the corresponding nodes within each group, and if all connections within the group fail, the next group will be accessed sequentially based on priority (the earlier in the order, the higher the priority).

URL parameter format:

```java
jdbc:yasdb:serverType://host1:port1,host2:port2;host3:port3,host4:port4/databasename

// When serverType=primary|standby, poolTimeout can be specified
jdbc:yasdb:serverType://host1:port1,host2:port2;host3:port3,host4:port4/databasename?poolTimeout=value
```

Introduction to configuration items is as follows:

- serverType: Optional parameter, service type, available options are [primary|standby|loadBalance|primaryLoadBalance|standbyLoadBalance]. If serverType is not specified, primary is used as default when entering multiple IPs.

    - primary: Indicates priority connection to the main node, JDBC will automatically identify the main node and connect.
    
    - standby: Indicates priority connection to the backup node, JDBC will automatically identify the backup node and connect.
    
    - loadBalance: Indicates load balancing connection, JDBC will automatically identify the node with the least number of connections and connect.
    
    - primaryLoadBalance: Indicates load balancing connection to the main node, JDBC will automatically identify the main node with the least number of connections and connect. If the number of connections is the same for main nodes, the one listed first in the URL connection configuration will be prioritized.

    - standbyLoadBalance: Indicates load balancing connection to the backup node, JDBC will automatically identify the backup node with the least number of connections and connect. If the number of connections is the same for backup nodes, the one listed first in the URL connection configuration will be prioritized.

- poolTimeout: Optional parameter, connection timeout in seconds when using primary or standby service types. The default value is 300 if poolTimeout is not specified.

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
