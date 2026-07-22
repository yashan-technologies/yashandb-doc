## Connecting to the Database

Use the `Open` function provided by the standard library `database/sql` to establish a database connection:

```go
func Open(driverName, dataSourceName string) (*DB, error)
```

Parameter Description:

|Parameter |Description |
| :------------- | :------------------------------------------------------- |
| driverName      | Database driver name. The Go driver name for YashanDB is `yasdb`. |
| dataSourceName  | Data source name. Configuration parameters for connecting to the database. |
| *DB             | Database connection object, representing a connection pool with zero or more database connections. |
| error           | Error information; if the connection is successful, the error value will be nil. |

***Example***

```go
package main
 
import (
    "database/sql"
    "log"
 
    _ "git.yasdb.com/go/yasdb-go"
)
func Connect() *sql.DB {
    db, err := sql.Open("yasdb", "sales/sales@127.0.0.1:1688")
    if err != nil {
        log.Fatalf("open yasdb error %s", err.Error())
    }
    return db
}
```

### Data Source Name Format

The data source name is a string used to connect to YashanDB, containing database username, password, server listening address, connection parameters, and other information. There are two types of data source names: TCP and UDS, as described below:

The format for TCP data source name is `user/password@url[?param1=value1&...&paramN=valueN]`.

* user: Username, required.
* password: User password, required.

    > **Note**: 
    >
    > If there are special characters `/`, `@`, `\` in `user` and `password`, they need to be escaped with the symbol `\`. For example, if `user` is `sys@132`, it needs to be escaped to `sys\\@132`.

* url: Server listening address. The format is as follows:

  * Single address connection: `host:port[/pdb_name]`.

  * Multi-address connection (primary type): `host:port,host:port,host:port[/pdb_name]` or `PRIMARY:host:port,host:port,host:port[/pdb_name]`.
  * Multi-address connection (loadBalance type): `LOADBALANCE:host:port,host:port,host:port[/pdb_name]`.
  Parameter meanings:
    * host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name. In YAC deployment, if [SCAN](../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../Database Administration/Cluster Management/VIP Management) has been configured, it can also be the corresponding domain name or IP address.
    * port: The listening port of the YashanDB server. If not adjusted during installation, the default is 1688. 
    * pdb_name: Only applicable to CDBs. Specifies connecting to a specific PDB. If omitted, defaults to connecting to the CDB root.
    * primary: The driver will connect to the nodes in the order of the specified listening addresses, determine the node roles by executing `SELECT * FROM DATABASE_ROLE`, and retain the connection established with the primary node for the first time.
    * loadBalance: The driver will randomly shuffle the specified listening addresses and then establish connections. It will obtain the current session count for each node and the node with the minimum session count as the target node (if there are multiple nodes with the same minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections.

    

>**Note**:
>
> - The failover efficiency of multiple groups of listener addresses is lower than that of multiple listener addresses. Grouping is mainly used to ensure that database connections are established as much as possible only through the first group of addresses. Please choose whether to group based on actual needs.
> - In YAC/Distributed Cluster Deployment, if [SCAN](../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../Database Administration/Cluster Management/VIP Management) has been configured, you can directly use the HA capabilities provided by the database server, and there is **no need** to configure an additional multi-address connection.
> - When configuring multiple addresses to connect to a YAC or a distributed cluster:
>   - In the case of single-cluster deployment, the driver will regard all its instances as primary nodes.
>   - In the case of primary-standby cluster deployment, the driver regards all instances in the primary cluster as primary nodes and all instances in the standby cluster as standby nodes. If load balancing is required in scenarios where SCAN or VIP is not configured, you can consider configuring multiple groups of address connections and specifying primaryLoadBalance or standbyLoadBalance as needed. All instances in the primary cluster form one group, and all instances in the standby cluster form another group.




* parameters: Connection configuration parameters, optional. The configurable parameters are as follows:

    |**Parameter Name** |**Default Value** |**Optional Range** |**Description** |
    | -------------------- | ---------------- | -------------------- | ------------------------------------------------------------------------------------- |
    | autocommit           | false             | true/false          | Whether to automatically commit transactions at the end of statements.          |
    | heartbeat_enable     | false             | true/false          | Whether to enable heartbeat detection functionality.                             |
    | number_as_string     | false             | true/false          | Determine whether to return the `NUMBER` type field as a string; default is to return as double. |
    | ukey_name            | -                 | Any string           | Specify the UKEY device name to be authenticated (e.g., `ukey_name=DBA`).     |
    | ukey_pin             | -                 | Any string           | Set the user password for UKEY (e.g., `ukey_pin=Yashan123`).                   |

The format for UDS data source name is `yasdb_data[?param1=value1&...&paramN=valueN]`, the UDS method can only connect to local database services, and the YASDBA user group must be configured for password-free access, defaulting to the sys user for password-free login.

* yasdb_data: Path of the local database service.

* parameters: Connection configuration parameters, same as for TCP.

### Data Source Name Examples

TCP data source names:

|Data Source Name |user |password |url |parameters |
| ---------------------------------------------------------------------------- | ------- | --------- | -------------------------------------------------------------- | ------------------------------------------ |
| sys/yasdb_123@127.0.0.1:1688                                                    | sys     | yasdb_123 | 127.0.0.1:1688                                                 | None                                       |
| sys\\\\@123/yasdb_123@127.0.0.1:1688                                         | sys@123 | yasdb_123 | 127.0.0.1:1688                                                 |                                            |
| sys/yasdb_123@127.0.0.1:1688?autocommit=true&number_as_string=true           | sys     | yasdb_123 | 127.0.0.1:1688                                                 | autocommit=true<br />number_as_string=true |
| sys/yasdb_123@192.168.0.1:1688,192.168.0.2:1688,192.168.0.3:1688                | sys     | yasdb_123 | 192.168.0.1:1688,192.168.0.2:1688,192.168.0.3:1688            | None                                       |
| sys/yasdb_123@loadBalance:192.168.0.1:1688,192.168.0.2:1688,192.168.0.3:1688    | sys     | yasdb_123 | loadBalance:192.168.0.1:1688,192.168.0.2:1688,192.168.0.3:1688 | None                                       |

UDS data source names:

|Data Source Name |user |password |yasdb_data |parameters |
| ------------------------------------------------------------- | ---- | -------- | ----------------------- | ------------------------------------------ |
| /home/yashan/yasdb_data                                            | sys  | Password-free | /home/yashan/yasdb_data | None                                       |
| /home/yashan/yasdb_data?autocommit=true&number_as_string=true    | sys  | Password-free | /home/yashan/yasdb_data | autocommit=true<br />number_as_string=true |

## Executing SQL

The function for executing SQL with the standard library `database/sql` is:

```go
func (db *DB) Exec(query string, args ...any) (Result, error)
```

Parameter Description:

|Parameter |Description |
| :----- | :-------------------------------------- |
| query     | The SQL command to execute, including delete, update, insert, etc. |
| args      | Represents the placeholder parameters in the query. |
| Result    | A summary of the executed SQL command. |
| error     | Error information.                       |

***Example***

```go
package main
 
import (
    "database/sql"
    "log"
 
    _ "git.yasdb.com/go/yasdb-go"
)

func Connect() *sql.DB {
    db, err := sql.Open("yasdb", "sales/sales@127.0.0.1:1688")
    if err != nil {
        log.Fatalf("open yasdb error %s", err.Error())
    }
    return db
}

func main(){
    db := Connect()
    result, err := db.Exec("CREATE TABLE users(id int, age int, name char(20))")
    if err != nil {
        log.Fatal(err)
    }
}
```

## Retrieving Result Sets

The functions for retrieving result sets with the standard library `database/sql` are:

```go
func (db *DB) Query(query string, args ...interface{}) (*Rows, error)
func (rs *Rows) Next() bool
func (rs *Rows) Scan(dest ...interface{}) error
```

Parameter Description:

|Parameter |Description |
| :---- | :-------------------------------------------------------------------------------------------- |
| query     | The SQL command for querying.                                                                    |
| args      | Represents the placeholder parameters in the query.                                              |
| *Rows     | The result of the query.                                                                          |
| dest      | Destination value.                                                                                |
| bool      | `Next` prepares for the next row result for the `Scan` method. Returns true if successful; false if there are no more rows or if an error occurs. |
| error     | Error information.                                                                                 |

***Example***

```go
package main
 
import (
    "database/sql"
    "log"
 
    _ "git.yasdb.com/go/yasdb-go"
)

func Connect() *sql.DB {
    db, err := sql.Open("yasdb", "sales/sales@127.0.0.1:1688")
    if err != nil {
        log.Fatalf("open yasdb error %s", err.Error())
    }
    return db
}

func main(){
    db := Connect() 
    age := 27
    rows, err := db.Query("SELECT name FROM users WHERE age=?", age)
    if err != nil {
        log.Fatal(err)
    }
    defer rows.Close()
    for rows.Next() { 
        var name string
        if err := rows.Scan(&name); err != nil { 
            log.Fatal(err)
        }
        fmt.Printf("%s is %d\n", name, age)
    }
    if err := rows.Err(); err != nil {
        log.Fatal(err)
    }
}
```
