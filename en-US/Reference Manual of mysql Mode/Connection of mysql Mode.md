Tools for connecting to YashanDB (mysql mode) include the YashanDB client (*yasql*), YashanDB JDBC driver, and YashanDB C driver, as well as MySQL ecosystem tools such as MySQL client, MySQL Connector/J, etc.

This section describes how to establish a connection to the database using a YashanDB client and MySQL clint.

For details on driver configurations and connection parameters, ​please ​refer to [JDBC Driver](./Quick Start/00Quick Start) and [C Driver](../Development Guide/C Language Family Drivers/C Driver/00C Driver).

> **Note**: 
>
> In mysql mode installation, all sessions created in the database OPEN phase default to mysql mode; sessions created during the NOMOUNT or MOUNT phases remain in yashan mode.

## Using *yasql* to Connect

### Connection Information

The connection information for the YashanDB server mainly includes authentication information and the server address. If connecting via a driver, the connection mode for multiple IP scenarios is also involved.

#### Authentication Information

Authentication information refers to the username and credentials used to log in to YashanDB.

|Format |Description |
|--------------------|-----------------------|
| username/password| Username and password for connecting and logging into the database.<br/>If the username is not enclosed in double quotes when created in yashan mode, the connection via *yasql* is case-insensitive; And in mysql mode, case sensitivity applies, and when connecting with the yasql tool, the username must be enclosed in double quotes.<br/>If the username or password contains special symbols (excluding underscores), certain rules may need to be applied for entering those symbols; for details, refer to the [yasql User Guide](../Tools Guide/yasql/User Guide for yasql). |
| / as sysdba| When logging in through [OS Authentication](../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) on the server where the database is hosted, the username and password can be omitted, but the `/` cannot be omitted, and `as sysdba` must be declared. |

#### Server Address

The server address can be a URL or an alias, which must correctly and uniquely point to the listening address of the target server (i.e., LISTEN_ADDR in the yashandb.toml configuration file).

|Information Type |Format |Description |
|--------------------|-----------------------|---------------|
| URL | @host:port<br/>or<br/>omit directly | <br/>* host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name.<br/>* port: Database server listening port. If not adjusted during installation, the default port for yashan mode is 1688, and for mysql mode when connecting through MySQL client or third-party tools, the default port is 1690 (Standalone (primary/standby) deployment) or 1691 (YAC deployment).<br/>* omitting directly (i.e., no URL): Indicates a connection to the database instance corresponding to the local YASDB_DATA environment variable. |
| Alias | @alias | Indicates connection using the alias defined in $YASDB_HOME/client/yasc_service.ini; an example of alias definition: `local = 127.0.0.1:1690`. |

#### Connection Modes (Multi-IP Scenarios)

The connection mode (multi-IP scenario) can only be configured when using the driver to connect to the YashanDB server. The mysql mode supports only the [JDBC Driver](../Development Guide/JDBC Driver/00JDBC Driver) and [C Driver](../Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect) , and requires that the YashanDB server has multiple nodes.

|Connection Mode |Format |Description |
|--------------------|-----------------------|---------------|
| primary mode | PRIMARY:host1:port,host2:port,…,hostn:port<br/>(`PRIMARY:` can be omitted) | Polling to connect to the primary database node in a high-availability deployment |
| standby mode |STANDBY:host1:port,host2:port,…,hostn:port | Polling to connect to the standby database node in a high-availability deployment |
| loadBalance mode | LOADBALANCE:host1:port,host2:port,…,hostn:port| Connecting to the optimal node (the node with the least connections is considered optimal) |
| primaryLoadBalance mode| PRIMARYLOADBALANCE:host1:port,host2:port,…,hostn:port | Connecting to the optimal primary database node |
| standbyLoadBalance mode| STANDBYLOADBALANCE:host1:port,host2:port,…,hostn:port | Load balancing to connect to the optimal standby database node |

### Prerequisites

- Complete the [YashanDB Client Installation](../Installation and Upgrade/Installation and Deployment/YashanDB Client Installation/00YashanDB Client Installation) and environment variable configuration. If connecting to the local database under the installation user on the server, there is no need to reinstall *yasql*.

- Ensure that the IP of the device initiating the connection is not on the [blacklist](../Product Security/Connection Management/Managing IP Blacklist or Whitelist).

### Connection Operations

To correctly pass the username to the database server, when connecting to YashanDB (mysql mode) with a user created under mysql mode via *yasql*, the username must be enclosed in double quotes.

- **Step-by-Step Login**

    Command format is as follows:

    ```shell
    # 1. Start *yasql*, open the login window
    $ yasql /nolog

    # 2. Use the connect or conn command to connect and log in to the database
    SQL> conn {authentication information} {server address}
    ```

    Example of logging in with the sales user:

    ```shell
    $ yasql /nolog
    YashanDB SQL Enterprise Edition Release {version_number} x86_64
    SQL> conn "sales"/********@192.168.1.2:1688

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL>
    ```

- **One-Click Login**

    Command format is as follows:

    ```shell
    # One-Step Login
    $ yasql [-S] {authentication information} {server address}  
    # Common usages are as follows:
    $ yasql [-S] username/password@host:port  
    $ yasql [-S] / as sysdba
    ```

    Example of logging in with the sales user:

    ```shell
    $ yasql \"sales\"/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    The appearance of the SQL command line prompt indicates a successful connection; if silent login, the SQL prompt will not appear.

- **Logging Out**

    ```sql
    exit
    ```

## Using MySQL Client to Connect

When connecting to YashanDB (mysql mode) using the MySQL client, logging in as the super administrator sys is not allowed.

When connecting to YashanDB (mysql mode) via third-Party tools, Please refer to [Connect to YashanDB via Third-Party Tools](./Quick Start/Basic SQL Operations) for details.

### Connection Information

- Authentication Information: Connect to the database service with a database user other than 'sys'.

- Server Address: When connecting to YashanDB using MySQL ecosystem tools, the default listening address for MySQL protocol (i.e., mysql_addr in the yashandb.toml configuration file) is used.

### Prerequisites

- Complete MySQL client installation and environment variable configuration.

- A regular user must be created.

- Ensure that the IP of the device initiating the connection is not on the [blacklist](../Product Security/Connection Management/Managing IP Blacklist or Whitelist).

### Connection Operations

- **Login**

    The login command format is as follows:

    ```shell
    $ mysql -h {host} -P {port} -u {username} -p{password} [-D {database_name}]
    ```

    Parameter introduction:

    - **-h {host}**: The IP address of the server where the database resides.

    - **-P {port}**: The listening port for the MySQL protocol on the YashanDB server (i.e., the port section of mysql_addr in the yashandb.toml configuration file). If not adjusted during installation, it defaults to 1690 (Standalone (primary/standby) deployment) or 1691 (YAC deployment).

    - **-u {username}**: The database username.

    - **-p{password}**: The password corresponding to the username.

    - **{database_name}**: Optional, specifies the default database for subsequent operations in the current session after connecting to YashanDB. If not specified, no database will be entered, and you can specify it using the [USE](./SQL Reference/SQL Statements/USE) statement after connecting.

    Example of logging in with the sales user:

    ```shell
    -- the default port is 1690 (Standalone (primary/standby) deployment) or 1691 (YAC deployment)
    $ mysql -h 192.168.1.2 -P 1690 -u sales -p********

    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 15
    Server version: {version information}

    ……

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql>
    ```

- **Logging Out**

    ```sql
    exit
    ```

## Change the Listener of MySQL service

When deploying YashanDB and configuring it for mysql mode, the service process will automatically start the MySQL listener upon initialization. This MySQL listener uses the `mysql_addr` parameter specified in the `yashandb.toml` configuration file during deployment as its listening port to accept connection requests via the MySQL protocol.

The MySQL listener service operates in a pluggable service architecture. This means the core database service operates independently without relying on plugin services. Plugin services are optional components that can be selectively loaded during database instance startup through configuration file settings.

### The Details of the Configuration File

- Path: $YASDB_DATA/config/service.ini

- File Format: Each pluggable service is configured in a dedicated line within the configuration file. A maximum of 9 services are supported, represented as `SERVICEn` where the index `n` ranges from 0 to 8.

- mysql Listener Format: SERVICE1 = {library = yas_my, name = mysql, args = "URL=127.0.0.1:1279,RSA_PRIVATE_FILE=$YASDB_DATA/mysqlkey/private_key.pem,RSA_PUBLIC_FILE=$YASDB_DATA/mysqlkey/public_key.pem"}

- Configuration Parameters Description:

    |Item |Description |Specification Value |
    | ------------ | -------- | --------- |
    | library | Path to the plugin's binary library | yas_my |
    | name | Name of the Pluggable Service| User-defined(max 64 bytes) |
    | args | Service-specific parameters (comma-separated) | It is defined by each plugin services |

    Configurable Parameters for MySQL Listener Service in `args`:

    |Name |Description |Specification Value |
    | ------------ | -------- | --------- |
    | URL | The address of MySQL Listener | IP:Port |
    | IPC | MySQL IPC communication socket|Absolute path to socket file |
    | RSA_PRIVATE_FILE | RSA private key file path | Absolute path |
    | RSA_PUBLIC_FILE | RSA public key file path | Absolute path |

    Generate RSA public/private keys using OpenSSL:

    ```shell
    # private key
    $ openssl genpkey -algorithm RSA -out private_key.pem -pkeyopt rsa_keygen_bits:2048
    
    # public key
    $ openssl rsa -pubout -in private_key.pem -out public_key.pem
    ```

### Activation Rules

Restart the database instance after modifying `service.ini`.
