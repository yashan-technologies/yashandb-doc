## Method

```python
connect( parameters… )
```

The connect() function is the constructor for creating database connection classes. The related parameters are shown in the table below.

|Parameter |Description |
| -------- | ------------------------------------------------------------ |
| dsn      | Data Source Name, which can include information such as the database username, password, and database URL. The full format is `user/password@url`. In a CDB, if you need to connect to a PDB, you can only specify the database URL through this parameter, and you must use a Python driver of version v23.5.1.100 or above.<br/><br/>URL format:<br>\* Single address connection: `host:port[/pdb_name]`<br>\* Multi-address connection (primary type): `host:port,host:port,host:port[/pdb_name]` or `PRIMARY:host:port,host:port,host:port[/pdb_name]`<br>\* Multi-address connection (loadBalance type): `LOADBALANCE:host:port,host:port,host:port[/pdb_name]`<br>Parameter meanings:<br> *host: Network address of the server where the database resides. It can be an IPv4 address, IPv6 address, or domain name. In a YAC deployment, if [SCAN](../../../../Database Administration/Cluster Management/SCAN Management.md) or [VIP](../../../../Database Administration/Cluster Management/VIP Management.md) is configured, the corresponding domain name or IP address can also be used.<br> *port: The listening port on the database server. If not modified during installation, the default is 1688.<br> *pdb_name: Only used in a CDB to specify a specific PDB to connect to. If omitted, the connection defaults to the root container.<br> *primary: The driver connects to nodes in the order of the specified listening addresses, executes SELECT * FROM DATABASE_ROLE to determine the node role, and retains the connection with the first primary node established.<br> *loadBalance: The driver shuffles the specified listening addresses randomly, connects to each node, retrieves the current session count for each node, selects the node with the minimum session count as the target node (if multiple nodes have the same minimum value, the first connected node is selected), retains the connection to the target node, and closes other connections. |
| user     | Database username.<br/>Optional parameter. If not specified in the dsn, it is mandatory.                                  |
| password | Database user password.<br/>Optional parameter. If not specified in the dsn, it is mandatory.                                 |


>**Note**:
>
> - The failover efficiency of grouped listening address connections is lower than that of multiple individual listening address connections. Grouping is mainly used to ensure that database connections are established only through the first group of addresses as much as possible. Please choose whether to use grouping based on actual requirements.
> - In YAC/distributed cluster deployments, if [SCAN](../../../../Database Administration/Cluster Management/SCAN Management.md) or [VIP](../../../../Database Administration/Cluster Management/VIP Management.md) is configured, you can directly use the high availability capabilities provided by the database server without needing to configure multi-address connections.
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



## Attributes

The Python DB API v2.0 specification requires that database modules define the following three attributes:

|Name |Meaning |Value |
| ------------ | -------------------------------------- | ----- |
| apilevel     | The Python DB API version supported by the module               | 2.0   |
| paramstyle   | The format style of parameter markers<br/>Currently fixed at named, meaning the format is `where name = :name` | named |
| threadsafety | The thread safety level supported by the module interface<br/>Currently fixed at 2, meaning threads can share modules and connections but not cursors             | 2     |

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

## Complete Example

```python
import yaspy

# Method 1: Connect using dsn parameter (recommended)
conn = yaspy.connect(
    dsn="sales/sales@192.168.1.2:1688"
)

# Method 2: Connect using dsn, user, password parameters
# conn = yaspy.connect(
#     dsn="192.168.1.2:1688",
#     user="sales",
#     password="sales"
# )

# Method 3: Multi-address connection (primary type, failover)
# conn = yaspy.connect(
#     dsn="sales/sales@192.168.1.1:1688,192.168.1.2:1688,192.168.1.3:1688"
# )

# Method 4: Multi-address connection (loadBalance type, load balancing)
# conn = yaspy.connect(
#     dsn="sales/sales@LOADBALANCE:192.168.1.1:1688,192.168.1.2:1688"
# )

# Create cursor
cursor = conn.cursor()

# Create test table
cursor.execute("DROP TABLE IF EXISTS test_yaspy")
cursor.execute("""
    CREATE TABLE test_yaspy (
        id INT PRIMARY KEY,
        name VARCHAR(50),
        value DECIMAL(10, 2)
    )
""")
conn.commit()
print('Table created successfully')

# Insert data
cursor.execute(
    "INSERT INTO test_yaspy VALUES (:id, :name, :value)",
    {"id": 1, "name": "Product A", "value": 99.99}
)
conn.commit()
print('Data inserted successfully')

# Query data
cursor.execute("SELECT * FROM test_yaspy WHERE id = :id", {"id": 1})
row = cursor.fetchone()
print(f'Retrieved: id={row[0]}, name={row[1]}, value={row[2]}')

# Clean up test table
cursor.execute("DROP TABLE test_yaspy")
conn.commit()
print('Table cleaned up')

# Close cursor and connection
cursor.close()
conn.close()
print('Connection closed')
```

Execute:

```python
Table created successfully
Data inserted successfully
Retrieved: id=1, name=Product A, value=99.99
Table cleaned up
Connection closed
```

Where:
- Table created successfully: Test table created successfully
- Data inserted successfully: Data inserted successfully
- Retrieved: id=1, name=Product A, value=99.99: Query result returned
- Table cleaned up: Test table cleaned up
- Connection closed: Connection closed
