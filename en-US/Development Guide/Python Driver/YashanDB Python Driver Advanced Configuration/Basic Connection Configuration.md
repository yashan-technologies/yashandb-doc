This chapter describes various connection configuration methods for the Python driver.

## Connection Parameters

> **Note**：
>
> - The `dsn` parameter of `yaspy.connect()` supports two formats:
>   - Full format: `user/password@host:port` (username and password combined in dsn)
>   - Simplified format: `host:port` (username and password specified through separate `user` and `password` parameters)
> - The `dsn` parameter of `SessionPool` **only supports** `host:port` format. Username and password must be specified through separate `user` and `password` parameters.

|Parameter |Description |
| ------------------ | -------------------- |
| dsn | Data Source Name. For connect(): supports `user/password@host:port` or `host:port` format; for SessionPool: only supports `host:port` format |
| user | Database username (mandatory for SessionPool; optional for connect(), required if not specified in dsn) |
| password | Database password (mandatory for SessionPool; optional for connect(), required if not specified in dsn) |

## Connection Methods

### Connect Using DSN (Recommended)

DSN is a connection method that combines connection information into a single string in the format "username/password@host:port". Suitable for most application scenarios.

```python
# Method 1: Include username and password directly in dsn
conn = yaspy.connect(dsn="sales/sales@192.168.1.2:1688")

# Method 2: dsn only contains host and port, username and password passed separately
conn = yaspy.connect(
    dsn="192.168.1.2:1688",
    user="sales",
    password="sales"
)
```

### Connect Using Connection Pool

Connection pool is a mechanism for managing database connections. By pre-establishing and maintaining a certain number of connection objects, it improves database access efficiency. Suitable for high-concurrency web applications and scenarios with frequent database operations, can reduce the overhead of connection creation and destruction.

```python
pool = yaspy.SessionPool(
    user="sales",
    password="sales",
    dsn="192.168.1.2:1688",
    min=1,
    max=4
)
conn = pool.acquire()
```