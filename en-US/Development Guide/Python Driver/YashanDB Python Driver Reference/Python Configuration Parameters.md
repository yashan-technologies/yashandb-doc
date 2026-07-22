This document describes the configuration parameters of the YashanDB Python driver.

**Alphabetical Index**

[A](#apara)	[D](#dpara)	[G](#gpara)	[I](#ipara)	[M](#mpara)	[MIN](#minpara)	[P](#ppara)	[T](#tpara)	[U](#upara)

<span id="apara" name="apara"></span>

### apiLevel

*   Parameter Type: String

*   Value Range/Format: String

*   Parameter Description: DB-API version. Currently fixed to "2.0", indicating support for Python DB API v2.0 specification.

### arraySize

*   Parameter Type: Int

*   Default Value: 100

*   Value Range/Format: Positive integer

*   Parameter Description: Default number of rows returned by fetchmany(). Used to specify the number of rows to fetch each time when batch fetching result sets.

<span id="dpara" name="dpara"></span>

### DSN

*   Parameter Type: String

*   Default Value: None

*   Value Range/Format: Data Source Name, which can include information such as the database username, password, and database URL.

*   Parameter Description: Data Source Name. This parameter can specify host, port, username, password, PDB name and other information. The URL format is as follows:
    * Single address connection: `host:port[/pdb_name]`
    * Multi-address connection (primary type): `host:port,host:port,host:port[/pdb_name]` or `PRIMARY:host:port,host:port,host:port[/pdb_name]`
    * Multi-address connection (loadBalance type): `LOADBALANCE:host:port,host:port,host:port[/pdb_name]`

> **Note**：
>
> - For `connect()`, the `dsn` parameter supports two formats: full format `user/password@url` (username and password combined in the dsn) or simple format `url` (username and password specified through separate `user` and `password` parameters).
> - For `SessionPool`, the `dsn` parameter **only supports** the `url` format (without the `user/password@` prefix). Username and password must be specified through separate `user` and `password` parameters. If the `user/password@url` format is used for SessionPool, the entire string will be parsed as the hostname, causing connection failure.

<span id="gpara" name="gpara"></span>

### getMode

*   Parameter Type: Int

*   Default Value: 1

*   Value Range/Format: Currently fixed to 1

*   Parameter Description: Connection acquisition mode. The specific behaviors are as follows: If there is an idle connection in the pool, directly get the connection; if the pool runs out of connections but the total number does not exceed the max value, new requests will create new connections based on the increment value; if the pool runs out of connections and the total number has reached the max value, an error is reported directly.

> **Note**：
>
> The `getMode` attribute is **not readable**. Accessing this attribute directly will raise `AttributeError` (e.g., `pool.getMode` or `pool.getmode` both do not exist). This parameter can only be set during `SessionPool` construction and is not supported by the `connect()` function.

<span id="ipara" name="ipara"></span>

### increment

*   Parameter Type: Int

*   Default Value: 1

*   Value Range/Format: Positive integer

*   Parameter Description: Number of connections to create at a time. When the pool runs out of available connections but the total number does not exceed the max value, new requests will create new connections based on the increment value.

<span id="mpara" name="mpara"></span>

### MAX

*   Parameter Type: Int

*   Default Value: 4

*   Value Range/Format: Positive integer, range is [min, 4294967295]

*   Parameter Description: Maximum number of connections in the connection pool.

<span id="minpara" name="minpara"></span>

### MIN

*   Parameter Type: Int

*   Default Value: 1

*   Value Range/Format: Positive integer, range is [0, 4294967295]

*   Parameter Description: Minimum number of connections in the connection pool.

<span id="ppara" name="ppara"></span>

### PASSWORD

*   Parameter Type: String

*   Default Value: None

*   Value Range/Format: String

*   Parameter Description: Database user password. Optional parameter. If not specified in the dsn parameter, it is mandatory.

### paramStyle

*   Parameter Type: String

*   Value Range/Format: String

*   Parameter Description: Parameter style. Currently fixed to `named`, indicating the use of named parameter style, where parameters are bound in SQL using `:name` or `:name=:value` format. **Positional binding with `?` is also supported**, using `?` as a placeholder to bind parameters in order.

<span id="tpara" name="tpara"></span>

### threadSafety

*   Parameter Type: Int

*   Value Range/Format: Integer

*   Parameter Description: Thread safety level supported by the module. Current value is 2, indicating that modules and connections can be shared across threads, but cursors cannot be shared across threads.

<span id="upara" name="upara"></span>

### USER

*   Parameter Type: String

*   Default Value: None

*   Value Range/Format: String

*   Parameter Description: Database username. Optional parameter. If not specified in the dsn parameter, it is mandatory.
