## Database Connection and Disconnection

The YashanDB Python driver communicates with the database through pooled connections or standalone connections.

- Pooled Connection: Multiple database connections are pre-created and stored in a connection pool. Applications retrieve connections from the pool when interacting with the database and return them after use. This enables efficient connection reuse, reducing overhead from frequent connection creation and closure. Suitable for high-concurrency scenarios, heavy database operations, or long-running applications. Only the yaspy module supports pooled connection.

- Standalone Connection: A new, independent connection is established each time the application interacts with the database, and closed after the operation completes. Suitable for small-scale applications, low concurrency scenarios, or short-term operations.

###  Pooled Connection

To use connection pooling, the YashanDB Python driver requires Python 3.6 or later.

####  Create a Connection Pool

Connection pools are managed via the SessionPool class, including configuring pool size, acquiring connections, and releasing connections.

```python
poolname = yaspy.SessionPool(
	user="username",
	password="password",
	dsn="dsn_string",
	min=2, 
	max=10,
	increment=1,
	getmode=0,
)
```

The parameters for creating a connection pool are shown in the table below.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| poolname      | Connection pool name.                                 |
| user      | Database username.  <br>Optional parameter. If not specified in the dsn parameter, it is mandatory.                   |
| password  | Database user password.  <br>Optional parameter. If not specified in the dsn parameter, it is mandatory.                           |
| dsn      | Data Source Name, which can include information such as the database username, password, and database URL. The full format is `user/password@url`. The URL format is as follows: <br>\* Single address connection: `host:port`<br>\* Multi-address connection (primary type): `host:port,host:port,host:port` or `PRIMARY:host:port,host:port,host:port`<br>\* Multi-address connection (loadBalance type): `LOADBALANCE:host:port,host:port,host:port`<br>Parameter meanings:<br>* host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name. In YAC deployment, if [SCAN](../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../Database Administration/Cluster Management/VIP Management) has been configured, it can also be the corresponding domain name or IP address. <br>\* port: The listening port of the YashanDB server. If not adjusted during installation, the default is 1688.  <br>\* primary: The driver will connect to the nodes in the order of the specified listening addresses, determine the node roles by executing `SELECT * FROM DATABASE_ROLE`, and retain the connection established with the primary node for the first time.<br/>\* loadBalance: The driver will randomly shuffle the specified listening addresses and then establish connections. It will obtain the current session count for each node and the node with the minimum session count as the target node (if there are multiple nodes with the same minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections.  |
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





####  Acquire a Connection

After creating the connection pool, call the acquire() method to retrieve a connection.

```python
connection = poolname.acquire()
```

####  Release a Connection

After completing database interactions, call the release() method to return the connection to the pool for reuse by other requests.

```python
poolname.release(connection)
```

####  Close the Connection Pool

Calling the close() method can immediately close the database connection pool, and all connections in the pool will no longer be available after successful execution.
	
```python
poolname.close()
```

###  Standalone Connection

A standalone connection establishes a single direct link between the driver and database via a connection object, which is also used to execute SQL, manage transactions, and close the connection.

####  Establish a Database Connection

When connecting to the database, use the connect() function provided by the python-yaspy module to establish a database connection. This returns a [connection object](./Status of Support for YashanDB Python Interfaces/Connection Object).

The method can be used as follows:

```python
# Using dsn, user, password parameters
connname = yaspy.connect(dsn=self.getDsn(), user=self.user, password=self.pwd)
# Using dsn parameter
connname = yaspy.connect(self.user+"/"+self.pwd+"@"+self.getDsn())
# Using dsn, password parameters
connname = yaspy.connect(self.user+"@"+self.getDsn(), password=self.pwd)
# Using host, port, user, password parameters
connname = yaspy.connect(host=self.host, port=self.port, user=self.user, password=self.pwd)
```

The optional parameters of the connect function are shown in the table below.

The related parameters are shown in the table below.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| connname      | Connection name.                                 |
| dsn       | Data Source Name, which can include information such as the database username, password, and database URL. The full format is `user/password@url`. The URL format is as follows: <br>\* Single address connection: `host:port`<br>\* Multi-address connection (primary type): `host:port,host:port,host:port` or `PRIMARY:host:port,host:port,host:port`<br>\* Multi-address connection (loadBalance type): `LOADBALANCE:host:port,host:port,host:port`<br>Parameter meanings:<br>* host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name. In YAC deployment, if [SCAN](../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../Database Administration/Cluster Management/VIP Management) has been configured, it can also be the corresponding domain name or IP address. <br>\* port: The listening port of the YashanDB server. If not adjusted during installation, the default is 1688.  <br>\* primary: The driver will connect to the nodes in the order of the specified listening addresses, determine the node roles by executing `SELECT * FROM DATABASE_ROLE`, and retain the connection established with the primary node for the first time.<br/>\* loadBalance: The driver will randomly shuffle the specified listening addresses and then establish connections. It will obtain the current session count for each node and the node with the minimum session count as the target node (if there are multiple nodes with the same minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections.  |
| user      | Database username.  <br>Optional parameter. If not specified in the dsn parameter, it is mandatory.                   |
| password  | Database user password.  <br>Optional parameter. If not specified in the dsn parameter, it is mandatory.                           |
| host      | host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name. In YAC deployment, if [SCAN](../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../Database Administration/Cluster Management/VIP Management) has been configured, it can also be the corresponding domain name or IP address. <br>Optional parameter. If not specified in the dsn parameter, it is mandatory. <br> If you need to configure multi - address connection, it can only be configured through the dsn parameter.                             |
| port      | Database port number.  <br>Optional parameter with a default value of 1688.           |





>**Note**:
>
> - The failover efficiency of multiple groups of listener addresses is lower than that of multiple listener addresses. Grouping is mainly used to ensure that database connections are established as much as possible only through the first group of addresses. Please choose whether to group based on actual needs.
> - In YAC/Distributed Cluster Deployment, if [SCAN](../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../Database Administration/Cluster Management/VIP Management) has been configured, you can directly use the HA capabilities provided by the database server, and there is **no need** to configure an additional multi-address connection.
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



#### Disconnect the Database Connection

Calling the close() method of the Connection can immediately close the database connection, and the connection will no longer be available after successful execution.

```python
connection.close()
```

## Execute SQL

Call the cursor() method of Connection to create and return a cursor object (Cursor), which can be used to execute statements and retrieve results.

```python
cursor =connection.cursor()
```

### Execute SQL Statement

Call the execute() method of Cursor to execute the SQL statement and use the commit() method to submit the pending transaction to the database.

```python
cursor.execute("drop table if exists example_table")
cursor.execute("create table example_table(id int , num int)")
cursor.execute("insert into example_table values(1,'test1')")
connection.commit()
```

### Execute Parameterized SQL Statement

```python
cursor.execute("insert into example_table values(?,?)",(2,'test2'))
data=(3,'test3')
cursor.execute("insert into example_table values(?,?)",data)
connection.commit()
```

### Close Cursor Object

After calling the close() method of Cursor, the cursor will no longer be available.

```python
cursor.close()
```
