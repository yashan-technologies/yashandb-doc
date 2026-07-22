The Python driver supports multi-address connections to achieve high availability and load balancing, including primary-standby connections, load balance connections, connection pool high availability and connection timeout configuration.

## Multi-Address Connection

### Primary-Standby Connection

Connect to multiple database nodes, automatically switch to standby node when primary node fails.

```python
# Primary-standby connection
dsn = "PRIMARY:192.168.1.1:1688,192.168.1.2:1688,192.168.1.3:1688"
conn = yaspy.connect(user="username", password="password", dsn=dsn)

# Or use shorthand format
dsn = "192.168.1.1:1688,192.168.1.2:1688,192.168.1.3:1688"
conn = yaspy.connect(user="username", password="password", dsn=dsn)
```

### Load Balance Connection

Use load balance mode, connections will be distributed across multiple nodes.

```python
# Load balance connection
dsn = "LOADBALANCE:192.168.1.1:1688,192.168.1.2:1688,192.168.1.3:1688"
conn = yaspy.connect(user="username", password="password", dsn=dsn)
```

## Connection Pool High Availability

Use multi-address connections in connection pool to achieve high availability.

```python
# Connection pool + high availability
pool = yaspy.SessionPool(
    user="username",
    password="password",
    dsn="192.168.1.1:1688,192.168.1.2:1688,192.168.1.3:1688",
    min=1,
    max=4,
    increment=1,
    getmode=1
)

# Acquire connection
conn = pool.acquire()

# Use connection (assuming example_table exists)
cursor = conn.cursor()
cursor.execute("SELECT * FROM example_table")
rows = cursor.fetchall()
print(f"Retrieved {len(rows)} rows")
```

## Timeout Configuration

> **Note**:
>
> Timeout parameters are parsed by the YAC layer and are not direct parameters of `yaspy.connect()`. The parameter names listed below (such as `connectTimeout`, `poolTimeout`) are for reference only. Actual supported timeout parameter names and value ranges need to be verified with YAC documentation. In some YAC versions these parameters may not be recognized, and calling them may result in error `YAS-08057 invalid connect param name`. It is recommended to test compatibility in a production environment before use.

### Connection Timeout

connectTimeout is used to set the socket connection timeout when establishing a connection, in seconds, with a default value of 10 seconds. The timeout configuration needs to be set in the DSN connection string:

```python
# Set connection timeout (configured in DSN via connection parameters)
# Note: In the current environment, connect_timeout / connectTimeout parameters may not be supported
# Calling may report YAS-08057 invalid connect param name
# It is recommended to test compatibility in production environment before use
conn = yaspy.connect(
    dsn="192.168.1.2:1688",
    user="sales",
    password="sales"
)
```

### Multi-address Connection Timeout

poolTimeout is used to set the timeout for multi-address connections, only applicable to multi-address connection scenarios (primary or standby type). During the entire connection process, even if all addresses are quickly traversed and none of them result in a successful connection, the system will still wait for the duration of poolTimeout before prompting a connection failure, with a default value of 300 seconds. The timeout configuration needs to be set in the DSN connection string:

```python
# In multi-address connection scenario, set pool timeout (configured in DSN via connection parameters)
# Note: In the current environment, pool_timeout / poolTimeout parameters may not be supported
# Calling may report YAS-08057 invalid connect param name
# It is recommended to test compatibility in production environment before use
conn = yaspy.connect(
    dsn="192.168.1.1:1688,192.168.1.2:1688,192.168.1.3:1688",
    user="sales",
    password="sales"
)
```
