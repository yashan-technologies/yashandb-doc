## yashan Mode

Tools for connecting to the YashanDB server (yashan mode) include the YashanDB client (*yasql*), YashanDB drivers, the Yashan Developer Center (YDC), and general third-party database management tools (such as DBeaver, Navicat), etc.
<span id="yashan" name="yashan" class="yaslink"></span>

### Connection Information

The connection information for the YashanDB server mainly includes authentication information and the server address. If connecting via a driver, the connection mode for multiple IP scenarios is also involved.

#### Authentication Information

Authentication information refers to the username and credentials used to log in to YashanDB.

|Format |Description |
|--------------------|-----------------------|
| username/password| Username and password for connecting and logging into the database.<br/>If the username is not enclosed in double quotes when created in Yashan mode, the connection via *yasql* is case-insensitive.<br/>If the username or password contains special symbols (excluding underscores), certain rules may need to be applied for entering those symbols; for details, refer to the [yasql User Guide](../../../工具手册/yasql/yasql使用指导). |
| / as sysdba| When logging in through [OS Authentication](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证) on the server where the database is hosted, the username and password can be omitted, but the `/` cannot be omitted, and `as sysdba` must be declared. |

#### Server Address

The server address can be a URL or an alias, which must correctly and uniquely point to the listening address of the target server (i.e., LISTEN_ADDR in the yashandb.toml configuration file).

|Information Type |Format |Description |
|--------------------|-----------------------|---------------|
| URL | @host:port<br/>or<br/>omit directly | <br/>* host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name.<br/>* port: The listening port of the YashanDB server. If not adjusted during installation, the default is 1688.<br/>* omitting directly (i.e., no URL): Indicates using [UDS local connection](../../../产品安全/连接管理/00连接管理) to connect to the database instance corresponding to the $YASDB_DATA environment variable. |
| Alias | @alias | Indicates connection using the alias defined in $YASDB_HOME/client/yasc_service.ini; an example of alias definition: `local = 127.0.0.1:1688`. |

#### Connection Modes (Multi-IP Scenarios)

Connection modes (multi-IP scenarios) can only be configured when connecting to the YashanDB server via a driver, such as the [JDBC driver](../../../Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction), [C driver](../../../Development Guide/C Language Family Drivers/C Driver/C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect), [OCI driver](../../../Development Guide/C Language Family Drivers/OCI Driver/OCI Driver Connection Configuration), and [ODBC driver](../../../Development Guide/C Language Family Drivers/ODBC Driver/ODBC Driver Installation/00ODBC Driver Installation), and requires multiple nodes on the YashanDB server.

|Connection Mode |Format |Description |
|--------------------|-----------------------|---------------|
| primary mode | PRIMARY:host1:port,host2:port,…,hostn:port<br/>(`PRIMARY:` can be omitted) | Polling to connect to the primary database node in a high-availability deployment |
| standby mode |STANDBY:host1:port,host2:port,…,hostn:port | Polling to connect to the standby database node in a high-availability deployment |
| loadBalance mode | LOADBALANCE:host1:port,host2:port,…,hostn:port| Connecting to the optimal node (the node with the least connections is considered optimal) |
| primaryLoadBalance mode| PRIMARYLOADBALANCE:host1:port,host2:port,…,hostn:port | Connecting to the optimal primary database node |
| standbyLoadBalance mode| STANDBYLOADBALANCE:host1:port,host2:port,…,hostn:port | Load balancing to connect to the optimal standby database node |

### Prerequisites

- Complete the [YashanDB Client Installation](../YashanDB Client Installation/00YashanDB Client Installation) and environment variable configuration. If connecting to the local database under the installation user on the server, there is no need to reinstall *yasql*.

- Ensure that the user has the [CREATE SESSION privilege](../../../Product Security/Data Access Control/Privilege and Role Management (yashan Mode)/System Privileges) (or [CONNECT role](../../../Product Security/Data Access Control/Privilege and Role Management (yashan Mode)/Roles)).

- Ensure that the IP of the device initiating the connection is not on the [blacklist](../../../Product Security/Connection Management/IP Blacklist and Whitelist Management).

### Connection Operations

This document only covers how to connect to the YashanDB server (yashan mode) using the YashanDB client (*yasql*). Connection methods for YashanDB drivers/toolkits will be detailed in corresponding documents.

- **One-Click Login**

    Command format is as follows:

    ```shell
    # One-Step Login
    $ yasql [-S] {authentication information} {server address}  
    # Common usages are as follows:
    $ yasql [-S] username/password@host:port  
    $ yasql [-S] / as sysdba
    ```

    `-S` means silent login, making sure `-S` is the first parameter. This parameter is case-insensitive. Using this way to log in and connect to the database does not return command-line prompts, command echoes, login prompts, or version information.

    Example of logging in with the sales user:
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version number} x86_64 - Linux

    SQL> 
    ```
    The appearance of the SQL command line prompt indicates a successful connection; if silent login, the SQL prompt will not appear.

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
    YashanDB SQL Enterprise Edition Release {version number} x86_64
    SQL> conn sales/********@192.168.1.2:1688

    Connected to:
    YashanDB Server Enterprise Edition Release {version number} x86_64 - Linux

    SQL>
    ```

- **Logging Out**

    ```sql
    exit
    ```

## mysql Mode

Tools for connecting to YashanDB (mysql mode) include the YashanDB client (*yasql*), YashanDB JDBC driver, and YashanDB C driver, as well as MySQL ecosystem tools such as MySQL client, MySQL Connector/J, etc.

> **Note**: 
>
> In mysql mode installation, all sessions created in the database OPEN phase default to mysql mode; sessions created during the NOMOUNT or MOUNT phases remain in yashan mode.

### Using *yasql* to Connect

#### Connection Information

Connection information is the same as [Yashan Mode](#yashan).

#### Prerequisites

- Complete the [YashanDB Client Installation](../YashanDB Client Installation/00YashanDB Client Installation) and environment variable configuration. If connecting to the local database under the installation user on the server, there is no need to reinstall *yasql*.

- Ensure that the IP of the device initiating the connection is not on the [blacklist](../../../Product Security/Connection Management/IP Blacklist and Whitelist Management).

#### Connection Operations

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
    YashanDB SQL Enterprise Edition Release {version number} x86_64
    SQL> conn "sales"/********@192.168.1.2:1688

    Connected to:
    YashanDB Server Enterprise Edition Release {version number} x86_64 - Linux

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

    Parameter introduction:

    - `-S`: Indicates silent login; make sure -S is the first parameter. This parameter is case-insensitive. Using this way to log in and connect to the database does not return command-line prompts, command echoes, login prompts, or version information.

    - {authentication information}, {server address}: For details, refer to [Connection Information](#yashan).

    Example of logging in with the sales user:

    ```shell
    $ yasql \"sales\"/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version number} x86_64 - Linux

    SQL> 
    ```
    The appearance of the SQL command line prompt indicates a successful connection; if silent login, the SQL prompt will not appear.

- **Logging Out**

    ```sql
    exit
    ```

### Using MySQL Client to Connect

When connecting to YashanDB (mysql mode) using the MySQL client, logging in as the super administrator sys is not allowed.

#### Connection Information

- Authentication Information: Same as [yashan mode](#yashan).

- Server Address: When connecting to YashanDB using MySQL ecosystem tools, the default listening address for MySQL protocol (i.e., mysql_addr in the yashandb.toml configuration file) is used.

#### Prerequisites

- Complete MySQL client installation and environment variable configuration.

- A regular user must be created.

- Ensure that the IP of the device initiating the connection is not on the [blacklist](../../../Product Security/Connection Management/IP Blacklist and Whitelist Management).

#### Connection Operations

- **Login**

    The login command format is as follows:

    ```shell
    $ mysql -h {host} -P {port} -u {username} -p{password} [-D {database_name}]
    ```

    Parameter introduction:

    - **-h {host}**: The IP address of the server where the database resides.

    - **-P {port}**: The listening port for the MySQL protocol on the YashanDB server (i.e., the port section of mysql_addr in the yashandb.toml configuration file). If not adjusted during installation, it defaults to 1690.

    - **-u {username}**: The database username.

    - **-p{password}**: The password corresponding to the username.

    - **{database_name}**: Optional, specifies the default database for subsequent operations in the current session after connecting to YashanDB. If not specified, no database will be entered, and you can specify it using the [USE](../../../Development Guide/SQL Reference Manual/SQL Statements (mysql Mode)/USE) statement after connecting.

    Example of logging in with the sales user:

    ```shell
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
