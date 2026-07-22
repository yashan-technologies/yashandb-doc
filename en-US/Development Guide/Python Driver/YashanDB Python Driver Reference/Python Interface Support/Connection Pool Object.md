The connection pool object is used to manage and reuse the connections between a Python application and a database.

Only the yaspy module supports pooled connection.

## Object Creation Method

The connection pool object must be created using `poolname = yaspy.SessionPool(parameters…)`. The parameters for creating a connection pool are shown in the table below.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| user     | Database username. Mandatory parameter.                                  |
| password | Database user password. Mandatory parameter.                                 |
| dsn      | Data Source Name, format is `host:port[/pdb_name]`. In a CDB, if you need to connect to a PDB, you can only specify the database URL through this parameter, and you must use a Python driver of version v23.5.1.100 or above.<br/><br/>**Note**: The `dsn` parameter for `SessionPool` **only supports** the `host:port` format (without the `user/password@` prefix). Username and password must be specified through separate `user` and `password` parameters. If the `user/password@url` format is used for SessionPool, the entire string will be parsed as the hostname, causing connection failure (YAS-00402).<br/><br/>URL format:<br>\* Single address connection: `host:port[/pdb_name]`<br>\* Multi-address connection (primary type): `host:port,host:port,host:port[/pdb_name]` or `PRIMARY:host:port,host:port,host:port[/pdb_name]`<br>\* Multi-address connection (loadBalance type): `LOADBALANCE:host:port,host:port,host:port[/pdb_name]`<br>Parameter meanings:<br> *host: Network address of the server where the database resides. It can be an IPv4 address, IPv6 address, or domain name. In a YAC deployment, if [SCAN](../../../../Database Administration/Cluster Management/SCAN Management.md) or [VIP](../../../../Database Administration/Cluster Management/VIP Management.md) is configured, the corresponding domain name or IP address can also be used.<br> *port: The listening port on the database server. If not modified during installation, the default is 1688.<br> *pdb_name: Only used in a CDB to specify a specific PDB to connect to. If omitted, the connection defaults to the root container.<br> *primary: The driver connects to nodes in the order of the specified listening addresses, executes SELECT * FROM DATABASE_ROLE to determine the node role, and retains the connection with the first primary node established.<br> *loadBalance: The driver shuffles the specified listening addresses randomly, connects to each node, retrieves the current session count for each node, selects the node with the minimum session count as the target node (if multiple nodes have the same minimum value, the first connected node is selected), retains the connection to the target node, and closes other connections.  |
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





## Connection Pool Methods

|Method |Description |
| ---------- | ------ |
| poolname.acquire()    | Retrieve a connection. |
| poolname.release()   | Return the connection to the pool. |
| poolname.close()    | Close the connection pool now. |

## Context Management Protocol

The connection pool object supports Python's context manager protocol, allowing the use of `with` statements to automatically manage connection acquisition and return.

```python
# Use with statement to manage connection pool connections
with pool.acquire() as conn:
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM table")
    for row in cursor:
        print(row)
# Connection automatically returned to pool
```

## Complete Example

```python
import yaspy

# Create connection pool
pool = yaspy.SessionPool(
    user="sales",
    password="sales",
    dsn="192.168.1.2:1688",
    min=1,
    max=4,
    increment=1
)
print('Connection pool created')
print(f'Pool status: min={pool.min}, max={pool.max}')

# 1. acquire() - Acquire connection
conn1 = pool.acquire()
print('Connection 1 acquired')

conn2 = pool.acquire()
print('Connection 2 acquired')

# Use connections for database operations
cursor1 = conn1.cursor()
cursor2 = conn2.cursor()

# Create test table
cursor1.execute("DROP TABLE IF EXISTS test_pool")
cursor1.execute("""
    CREATE TABLE test_pool (
        id INT PRIMARY KEY,
        name VARCHAR(50)
    )
""")
conn1.commit()
print('Table created')

# Use connection 1 to insert data
cursor1.execute("INSERT INTO test_pool VALUES (1, 'Product A')")
cursor1.execute("INSERT INTO test_pool VALUES (2, 'Product B')")
conn1.commit()
print('Data inserted via connection 1')

# Use connection 2 to query data
cursor2.execute("SELECT * FROM test_pool ORDER BY id")
rows = cursor2.fetchall()
print(f'Queried via connection 2: {len(rows)} records:')
for row in rows:
    print(f'  id={row[0]}, name={row[1]}')

# 2. release() - Release connection
pool.release(connection=conn1)
print('Connection 1 released')

pool.release(connection=conn2)
print('Connection 2 released')

# Acquire connection again
conn3 = pool.acquire()
print('Connection 3 acquired (reused from pool)')

cursor3 = conn3.cursor()
cursor3.execute("SELECT * FROM test_pool")
rows = cursor3.fetchall()
print(f'Connection 3 query result: {len(rows)} records')

# Clean up test table
cursor3.execute("DROP TABLE test_pool")
conn3.commit()
print('Table cleaned up')

# Close cursor and release connection
cursor3.close()
pool.release(connection=conn3)
print('Connection 3 released')

# 3. close() - Close connection pool
pool.close()
print('Connection pool closed')
```

> **Note**：
>
> This example uses `len(rows)` to count SELECT results after `fetchall()`. `rowcount` is 0 immediately after query execution before any data is fetched; during the fetching process, it accumulates the number of rows already fetched, so it is not suitable to use `rowcount` to represent the total number of rows in the result set when no data has been fetched yet.

Execute:

```python
Connection pool created
Pool status: min=1, max=4
Connection 1 acquired
Connection 2 acquired
Table created
Data inserted via connection 1
Queried via connection 2: 2 records:
  id=1, name=Product A
  id=2, name=Product B
Connection 1 released
Connection 2 released
Connection 3 acquired (reused from pool)
Connection 3 query result: 2 records
Table cleaned up
Connection 3 released
Connection pool closed
```

Where:
- Connection pool created: Connection pool created successfully
- Pool status: min=1, max=4: Connection pool status (min 1, max 4 connections)
- Connection 1 acquired: Acquired connection 1 from pool
- Connection 2 acquired: Acquired connection 2 from pool
- Table created: Test table created successfully
- Data inserted via connection 1: Data inserted successfully via connection 1
- Queried via connection 2: Queried via connection 2
- Connection 1 released: Connection 1 returned to pool
- Connection 2 released: Connection 2 returned to pool
- Connection 3 acquired (reused from pool): Acquired connection 3 (reused from pool)
- Connection 3 query result: Connection 3 query result
- Table cleaned up: Test table cleaned up
- Connection 3 released: Connection 3 returned to pool
- Connection pool closed: Connection pool closed
