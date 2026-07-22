The YashanDB Python driver communicates with the database through pooled connections or standalone connections.

- Standalone Connection: A new, independent connection is established each time the application interacts with the database, and closed after the operation completes. Suitable for small-scale applications, low concurrency scenarios, or short-term operations.

- Pooled Connection: Multiple database connections are pre-created and stored in a connection pool. Applications retrieve connections from the pool when interacting with the database and return them after use. This enables efficient connection reuse, reducing overhead from frequent connection creation and closure. Suitable for high-concurrency scenarios, heavy database operations, or long-running applications. Only the yaspy module supports pooled connection.

## Standalone Connection

A standalone connection establishes a single direct link between the driver and database via a connection object, which is also used to execute SQL, manage transactions, and close the connection.

### Establish a Database Connection

When connecting to the database, use the connect() function provided by the python-yaspy module to establish a database connection. This returns a Connection object.

The method can be used as follows:

```python
# Method 1: Using dsn parameter (recommended)
conn = yaspy.connect(dsn="sales/sales@192.168.1.2:1688")

# Method 2: Using dsn, user, password parameters
conn = yaspy.connect(dsn="192.168.1.2:1688", user="sales", password="sales")
```

The related parameters are shown in the table below.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| dsn      | Data Source Name, format is `host:port[/pdb_name]`.<br/><br/>URL format:<br>\* Single address connection: `host:port[/pdb_name]`<br>\* Multi-address connection (primary type): `host:port,host:port,host:port[/pdb_name]` or `PRIMARY:host:port,host:port,host:port[/pdb_name]`<br>\* Multi-address connection (loadBalance type): `LOADBALANCE:host:port,host:port,host:port[/pdb_name]`<br>**Note**: The `dsn` parameter for `SessionPool` **only supports** the `host:port` format (without the `user/password@` prefix). Username and password must be specified through separate `user` and `password` parameters.<br/><br/>Parameter meanings:<br> *host: Network address of the server where the database resides. It can be an IPv4 address, IPv6 address, or domain name. In a YAC deployment, if [SCAN](../../../Database Administration/Cluster Management/SCAN Management.md) or [VIP](../../../Database Administration/Cluster Management/VIP Management.md) is configured, the corresponding domain name or IP address can also be used.<br> *port: The listening port on the database server. If not modified during installation, the default is 1688.<br> *pdb_name: Only used in a CDB to specify a specific PDB to connect to. If omitted, the connection defaults to the root container.<br> *primary: The driver connects to nodes in the order of the specified listening addresses, executes SELECT * FROM DATABASE_ROLE to determine the node role, and retains the connection with the first primary node established.<br> *loadBalance: The driver shuffles the specified listening addresses randomly, connects to each node, retrieves the current session count for each node, selects the node with the minimum session count as the target node (if multiple nodes have the same minimum value, the first connected node is selected), retains the connection to the target node, and closes other connections.  |
| user     | Database username.<br/>Optional parameter. If not specified in the dsn, it is mandatory.                                  |
| password | Database user password.<br/>Optional parameter. If not specified in the dsn, it is mandatory.                                 |

>**Note**:
>
> - The failover efficiency of grouped listening address connections is lower than that of multiple individual listening address connections. Grouping is mainly used to ensure that database connections are established only through the first group of addresses as much as possible. Please choose whether to use grouping based on actual requirements.
> - In YAC/distributed cluster deployments, if [SCAN](../../../Database Administration/Cluster Management/SCAN Management.md) or [VIP](../../../Database Administration/Cluster Management/VIP Management.md) is configured, you can directly use the high availability capabilities provided by the database server without needing to configure multi-address connections.
> - When configuring multi-address connections for YAC/distributed cluster:
>   - For a single cluster deployment, the driver treats all instances as primary nodes.
>   - For a primary-standby cluster deployment, the driver treats all instances of the primary cluster as primary nodes and all instances of the standby cluster as standby nodes. To achieve load balancing in scenarios without SCAN or VIP configured, consider configuring multiple groups of address connections and specifying primaryLoadBalance or standbyLoadBalance as needed, with all instances of the primary cluster as one group and all instances of the standby cluster as another group.



If there are special characters in user and password such as /, @, \\, they need to be escaped using the symbol \\, as shown below:

| dsn                                       | user   | password   | host      | port |
| ----------------------------------------- | ------ | ---------- | --------- | ---- |
| 192.168.1.2:1688                            | Not Specified | Not Specified | 192.168.1.2 | 1688 |
| sys@192.168.1.2:1688                        | sys    | Not Specified | 192.168.1.2 | 1688 |
| sys/yasdb_123@192.168.1.2:1688              | sys    | yasdb_123  | 192.168.1.2 | 1688 |
| sys/yasdb\\@_123@192.168.1.2:1688           | sys    | yasdb@_123 | 192.168.1.2 | 1688 |
| sys\\//yasdb\\@_123@192.168.1.2:1688        | sys/   | yasdb@_123 | 192.168.1.2 | 1688 |
| s\\/ys\\@\\\\/yasdb\\@_123@192.168.1.2:1688 | s/ys@\\ | yasdb@_123 | 192.168.1.2 | 1688 |

> **Note**:
>
> In the DSN escape table above, the "user" and "password" columns marked as "Not Specified" only indicate YAC layer parsing semantics. When using `yaspy.connect`, dsn, user, and password **must all be non-null**. If user/password is not specified in the DSN, you must provide them via the `user`/`password` parameters, otherwise an `invalid connect info` error will be raised.

### Complete Example

```python
import yaspy

# Method 1: Connect using dsn parameter (recommended)
try:
    conn = yaspy.connect(dsn="sales/sales@192.168.1.2:1688")
    print("Connection established successfully!")
    # Execute database operations
    cursor = conn.cursor()
    cursor.execute("SELECT SYSDATE FROM DUAL")
    result = cursor.fetchone()
    print(f"Current database time: {result[0]}")
    # Close cursor and connection
    cursor.close()
    conn.close()
    print("Connection closed.")
except Exception as e:
    print(f"Connection failed: {e}")

# Method 2: Connect using dsn, user, password parameters
try:
    conn = yaspy.connect(
        dsn="192.168.1.2:1688",
        user="sales",
        password="sales"
    )
    print("Connection established successfully via DSN!")
    cursor = conn.cursor()
    cursor.execute("SELECT SYSDATE FROM DUAL")
    result = cursor.fetchone()
    print(f"Current database time: {result[0]}")
    cursor.close()
    conn.close()
except Exception as e:
    print(f"Connection failed: {e}")
```

Execution:

```python
$ python connect_example.py
Connection established successfully!
Current database time: 2026-05-12
Connection closed.
```

## Pooled Connection

A connection pool is a technology for managing database connections. It establishes and maintains a set of connections in advance, reducing performance overhead caused by frequent creation and destruction of connections, and improving application response efficiency. Connection pools are suitable for high-concurrency scenarios and can effectively enhance database access performance.

### Create a Connection Pool

Connection pools are managed via the SessionPool class, including configuring pool size, acquiring connections, and releasing connections.

The parameters for creating a connection pool are shown in the table below.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| user      | Database username. Mandatory parameter.                   |
| password  | Database user password. Mandatory parameter.  |
| dsn      | Data Source Name, which can include information such as the database username, password, and database URL. The full format is `user/password@url`. In a CDB, if you need to connect to a PDB, you can only specify the database URL through this parameter, and you must use a Python driver of version v23.5.1.100 or above.<br/><br/>URL format:<br>\* Single address connection: `host:port[/pdb_name]`<br>\* Multi-address connection (primary type): `host:port,host:port,host:port[/pdb_name]` or `PRIMARY:host:port,host:port,host:port[/pdb_name]`<br>\* Multi-address connection (loadBalance type): `LOADBALANCE:host:port,host:port,host:port[/pdb_name]`<br>Parameter meanings:<br> *host: Network address of the server where the database resides. It can be an IPv4 address, IPv6 address, or domain name. In a YAC deployment, if [SCAN](../../../Database Administration/Cluster Management/SCAN Management.md) or [VIP](../../../Database Administration/Cluster Management/VIP Management.md) is configured, the corresponding domain name or IP address can also be used.<br> *port: The listening port on the database server. If not modified during installation, the default is 1688.<br> *pdb_name: Only used in a CDB to specify a specific PDB to connect to. If omitted, the connection defaults to the root container.<br> *primary: The driver connects to nodes in the order of the specified listening addresses, executes SELECT * FROM DATABASE_ROLE to determine the node role, and retains the connection with the first primary node established.<br> *loadBalance: The driver shuffles the specified listening addresses randomly, connects to each node, retrieves the current session count for each node, selects the node with the minimum session count as the target node (if multiple nodes have the same minimum value, the first connected node is selected), retains the connection to the target node, and closes other connections.  |
| min      | Minimum number of connections in the connection pool, an optional parameter, positive integer with a default value of 1.          |
| max      | Maximum number of connections in the connection pool, an optional parameter, positive integer with a default value of 4.                               |
| increment      | The number of connections to create at once, an optional parameter with a default value of 1. <br/>When the pool runs out of available connections but the total number of connections does not exceed the `max` value, new requests will create new connections based on the `increment` value.           |
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

> **Note**:
>
> In the DSN escape table above, the "user" and "password" columns marked as "Not Specified" only indicate YAC layer parsing semantics. When using `yaspy.connect`, dsn, user, and password **must all be non-null**. If user/password is not specified in the DSN, you must provide them via the `user`/`password` parameters, otherwise an `invalid connect info` error will be raised.

```python
import yaspy

pool = yaspy.SessionPool(
    user="sales",
    password="sales",
    dsn="192.168.1.2:1688",
    min=1,
    max=4,
    increment=1,
    getmode=1,
)
```

### Acquire a Connection

After creating the connection pool, call the acquire() method to retrieve a connection.


```python
conn = pool.acquire()
# Execute database operations
cursor = conn.cursor()
cursor.execute("SELECT SYSDATE FROM DUAL")
result = cursor.fetchone()
print(f"Current database time: {result[0]}")
```

### Release a Connection

After completing database interactions, call the release() method to return the connection to the pool for reuse by other requests.

> **Note**:
>
> `release()` is recommended to use the `connection=` keyword parameter, but also supports positional parameter syntax `pool.release(conn)`.

```python
pool.release(connection=connection)
# Or: pool.release(connection)
```

### Complete Example

```python
from decimal import Decimal
import yaspy

pool = yaspy.SessionPool(
	user="sales",
	password="sales",
	dsn="192.168.1.2:1688",
	min=2,
	max=10,
	increment=1,
	getmode=1,
)
connection = pool.acquire()
cursor=connection.cursor()

cursor.execute("drop table if exists bind")
cursor.execute("create table bind(id int , name varchar(256))")

def bind_param():
    cursor = connection.cursor()
    cursor.execute("drop table if exists bind_param_heap_1")
    cursor.execute("create table bind_param_heap_1(a int, b double, c int)")
    cursor.execute("insert into bind_param_heap_1 values(:1, :2, 30)",(1,10))
    cursor.execute("insert into bind_param_heap_1 values(:1, :2, 40)",(2,20))
    connection.commit()
    cursor.execute("select * from bind_param_heap_1")
    result = cursor.fetchmany(2)
    print(result)
    cursor.execute("delete from bind_param_heap_1 where a=:1",(1,))
    connection.commit()
    cursor.execute("select * from bind_param_heap_1")
    result = cursor.fetchall()
    print(result)
    cursor.execute("update bind_param_heap_1 set c=:1", (50,))
    connection.commit()
    cursor.execute("select * from bind_param_heap_1")
    result = cursor.fetchone()
    print(result)

bind_param()

cursor.close()
pool.release(connection=connection)
pool.close()
```

Execution:

```python
python3 Example.py
```
