The connection pool object is used to manage and reuse the connections between a Python application and a database.

Only the yaspy module supports pooled connection.

##  Object Creation Method

The connection pool object must be created using `poolname = yaspy.SessionPool(parameters…)`. The parameters for creating a connection pool are shown in the table below.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| poolname      | Connection pool name.                                 |
| user      | Database username.  <br>Optional parameter. If not specified in the dsn parameter, it is mandatory.                   |
| password  | Database user password.  <br>Optional parameter. If not specified in the dsn parameter, it is mandatory.                           |
| dsn      | Data Source Name, which can include information such as the database username, password, and database URL. The full format is `user/password@url`. The URL format is as follows: <br>\* Single address connection: `host:port`<br>\* Multi-address connection (primary type): `host:port,host:port,host:port` or `PRIMARY:host:port,host:port,host:port`<br>\* Multi-address connection (loadBalance type): `LOADBALANCE:host:port,host:port,host:port`<br>Parameter meanings:<br>* host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name. In YAC deployment, if [SCAN](../../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../../Database Administration/Cluster Management/VIP Management) has been configured, it can also be the corresponding domain name or IP address. <br>\* port: The listening port of the YashanDB server. If not adjusted during installation, the default is 1688.  <br>\* primary: The driver will connect to the nodes in the order of the specified listening addresses, determine the node roles by executing `SELECT * FROM DATABASE_ROLE`, and retain the connection established with the primary node for the first time.<br/>\* loadBalance: The driver will randomly shuffle the specified listening addresses and then establish connections. It will obtain the current session count for each node and the node with the minimum session count as the target node (if there are multiple nodes with the same minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections.  |
| min      | Minimum number of connections in the connection pool, an optional parameter ranging from (0,4294967295] with a default value of 2.          |
| max      | Maximum number of connections in the connection pool, an optional parameter ranging from [min, 4294967295] with a default value of 10.                               |
| increment      | The number of connections to create at once when there are insufficient connections, an optional parameter ranging from [min, 4294967295] with a default value of 1. <br/>When the pool runs out of available connections but the total number of connections does not exceed the `max` value, new requests will create new connections based on the `increment` value.           |
| getmode      | Connection acquisition mode. Optional parameter; currently fixed at 1. Behavior specifics: <br/> - If idle connections exist in the pool, a connection is obtained directly. <br/> - If all connections in the pool are exhausted but the total connection count has not yet reached the `max` limit, new requests will create additional connections according to the `increment` value. <br/> - If all connections in the pool are exhausted and the total connection count has already reached the `max` limit, an error is returned directly.     |



If there are special characters in user and password such as /, @, \\, they need to be escaped using the symbol \\, as shown below:

| dsn                                       | user   | password   | host      | port |
| ----------------------------------------- | ------ | ---------- | --------- | ---- |
| 192.168.1.2:1688                            | Not Specified | Not Specified | 192.168.1.2 | 1688 |
| sys@192.168.1.2:1688                        | sys    | Not Specified | 192.168.1.2 | 1688 |
| sys/yasdb_123@192.168.1.2:1688              | sys    | yasdb_123  | 192.168.1.2 | 1688 |
| sys/yasdb\\@_123@192.168.1.2:1688           | sys    | yasdb@_123 | 192.168.1.2 | 1688 |
| sys\\//yasdb\\@_123@192.168.1.2:1688        | sys/   | yasdb@_123 | 192.168.1.2 | 1688 |
| s\\/ys\\@\\\\/yasdb\\@_123@192.168.1.2:1688 | s/ys@\\ | yasdb@_123 | 192.168.1.2 | 1688 |





##  Connection Pool Methods

|Method | Description  |
| ---------- | ------ |
| poolname.acquire()    | Retrieve a connection. |
| poolname.release()   | Return the connection to the pool. |
| poolname.close()    | Close the connection pool now. |
