##  Method

```python
connect( parameters… )
```

The connect() method is the constructor for creating database connection classes. The related parameters are shown in the table below.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| connname      | Connection name.                                 |
| dsn       | Data Source Name, which can include information such as the database username, password, and database URL. The full format is `user/password@url`. The URL format is as follows: <br>\* Single address connection: `host:port`<br>\* Multi-address connection (primary type): `host:port,host:port,host:port` or `PRIMARY:host:port,host:port,host:port`<br>\* Multi-address connection (loadBalance type): `LOADBALANCE:host:port,host:port,host:port`<br>Parameter meanings:<br>* host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name. In YAC deployment, if [SCAN](../../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../../Database Administration/Cluster Management/VIP Management) has been configured, it can also be the corresponding domain name or IP address. <br>\* port: The listening port of the YashanDB server. If not adjusted during installation, the default is 1688.  <br>\* primary: The driver will connect to the nodes in the order of the specified listening addresses, determine the node roles by executing `SELECT * FROM DATABASE_ROLE`, and retain the connection established with the primary node for the first time.<br/>\* loadBalance: The driver will randomly shuffle the specified listening addresses and then establish connections. It will obtain the current session count for each node and the node with the minimum session count as the target node (if there are multiple nodes with the same minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections.  |
| user      | Database username.  <br>Optional parameter. If not specified in the dsn parameter, it is mandatory.                   |
| password  | Database user password.  <br>Optional parameter. If not specified in the dsn parameter, it is mandatory.                           |
| host      | host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name. In YAC deployment, if [SCAN](../../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../../Database Administration/Cluster Management/VIP Management) has been configured, it can also be the corresponding domain name or IP address. <br>Optional parameter. If not specified in the dsn parameter, it is mandatory. <br> If you need to configure multi - address connection, it can only be configured through the dsn parameter.                             |
| port      | Database port number.  <br>Optional parameter with a default value of 1688.           |





>**Note**:
>
> - The failover efficiency of multiple groups of listener addresses is lower than that of multiple listener addresses. Grouping is mainly used to ensure that database connections are established as much as possible only through the first group of addresses. Please choose whether to group based on actual needs.
> - In YAC/Distributed Cluster Deployment, if [SCAN](../../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../../Database Administration/Cluster Management/VIP Management) has been configured, you can directly use the HA capabilities provided by the database server, and there is **no need** to configure an additional multi-address connection.
> - When configuring multiple addresses to connect to a YAC or a distributed cluster:
>   - In the case of single-cluster deployment, the driver will regard all its instances as primary nodes.
>   - In the primary-standby cluster deployment, the driver regards all instances in the primary cluster as primary nodes and all instances in the standby cluster as standby nodes. If load balancing is required in scenarios where SCAN or VIP is not configured, you can consider configuring multiple groups of address connections and specifying primaryLoadBalance or standbyLoadBalance as needed. All instances in the primary cluster form one group, and all instances in the standby cluster form another group.






If there are special characters in user and password such as /, @, \\, they need to be escaped using the symbol \\, as shown below:

| dsn                                       | user   | password   | host      | port |
| ----------------------------------------- | ------ | ---------- | --------- | ---- |
| 192.168.1.2:1688                            | Not Specified | Not Specified | 192.168.1.2 | 1688 |
| sys@192.168.1.2:1688                        | sys    | Not Specified | 192.168.1.2 | 1688 |
| sys/yasdb_123@192.168.1.2:1688              | sys    | yasdb_123  | 192.168.1.2 | 1688 |
| sys/yasdb\\@_123@192.168.1.2:1688           | sys    | yasdb@_123 | 192.168.1.2 | 1688 |
| sys\\//yasdb\\@_123@192.168.1.2:1688        | sys/   | yasdb@_123 | 192.168.1.2 | 1688 |
| s\\/ys\\@\\\\/yasdb\\@_123@192.168.1.2:1688 | s/ys@\\ | yasdb@_123 | 192.168.1.2 | 1688 |



##  Attributes

The Python DB API v2.0 specification requires that database modules define the following three attributes:

|Name |Meaning |Value |
| ------------ | -------------------------------------- | ----- |
| apilevel    | The Python DB API version supported by the module | 2.0    |
| paramstyle  | The type of parameter marker formatting expected by the interface <br/> Currently named as in `where name = :name` | named  |
| threadsafety | The level of thread safety supported by the module <br/> Currently 2, which means that threads may share the module and connections, but not cursors | 2      |

Python developers can check the values of the variables using the following code:

```python
>>> import yaspy
>>> yaspy.threadsafety
2
>>> yaspy.apilevel
'2.0'
>>> yaspy.paramstyle
'named'
```
