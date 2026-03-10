The connection pool object is used to manage and reuse the connections between a Python application and a database.

Only the yaspy module supports pooled connection.

##  Object Creation Method

The connection pool object must be created using `poolname = yaspy.SessionPool(parameters…)`. The parameters for creating a connection pool are shown in the table below.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| poolname      | Connection pool name.                                 |
| user      | Database username.                                 |
| password  | Database user password.                           |
| dsn      | Data source name, which may include database username, password, IP address, port number, and other information. The format is as follows:<br>\* Standalone/single IP mode: host:port<br>\* HA multi-IP primary mode: host:port,host:port,host:port or PRIMARY:host:port,host:port,host:port<br>\* Multi-IP load balance mode: LOADBALANCE:host:port,host:port,host:port<br>Parameter meanings:<br>\* host: The server domain name or IP address, which must be configured as the address of the standalone instance server or the distributed server.<br>\* port: The database service port, such as 1688.<br>\* primary: This keyword indicates that the primary mode is used for connecting in multi-IP, which is the default mode for multi-IP and can omit this keyword. In this mode, it will poll to connect to the HA primary database but not connect to the standby database.<br>\* loadBalance: This keyword indicates that the load balance mode is used for connecting in multi-IP. In this mode, it will connect to the optimal node (i.e., the node with the fewest connections). |
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
