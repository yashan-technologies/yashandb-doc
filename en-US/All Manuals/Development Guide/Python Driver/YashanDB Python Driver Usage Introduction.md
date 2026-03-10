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
| dsn       | Data source name, which may include database username, password, IP address, port number, and other information. The format is as follows:<br>\* Standalone/single IP mode: host:port<br>\* HA multi-IP primary mode: host:port,host:port,host:port or PRIMARY:host:port,host:port,host:port<br>\* Multi-IP load balance mode: LOADBALANCE:host:port,host:port,host:port<br>Parameter meanings:<br>\* host: The server domain name or IP address, which must be configured as the address of the standalone instance server or the distributed server.<br>\* port: The database service port, such as 1688.<br>\* primary: This keyword indicates that the primary mode is used for connecting in multi-IP, which is the default mode for multi-IP and can omit this keyword. In this mode, it will poll to connect to the HA primary database but not connect to the standby database.<br>\* loadBalance: This keyword indicates that the load balance mode is used for connecting in multi-IP. In this mode, it will connect to the optimal node (i.e., the node with the fewest connections). |
| user      | Database username, optional parameter.                                 |
| password  | Database user password, optional parameter.                           |
| host      | Database IP addres, optional parameter.                              |
| port      | Database port number, optional parameter with a default value of 1688.           |



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
