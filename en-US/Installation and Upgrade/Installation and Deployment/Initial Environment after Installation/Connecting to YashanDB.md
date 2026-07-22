## Connection Information



The connection information of the YashanDB server mainly includes authentication information and the server address. If using a driver for connection, HA connection may also be involved.

### Authentication Information

Authentication information refers to the username and credentials used to log in to YashanDB.

|Format |Description |
|--------------------|-----------------------|
| username/password| Username and password for connecting and logging into the database.<br/>If the username is not enclosed in double quotes when created in yashan mode, the connection via *yasql* is case-insensitive.<br/>If the username or password contains special characters related to OS commands (e.g., `@`, `/`, `.`, `!`, `$`, `'`, etc.), they must be escaped. For details, please refer to [yasql Usage Guide](../../../Tools Guide/yasql/User Guide for yasql). |
| / as sysdba| When logging in through [Operating System Authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) on the server where the database is hosted, the username and password can be omitted, but the `/` cannot be omitted, and `as sysdba` must be declared. |

<span id="URL" name="URL"></span>

### Server Address

The server address can be a URL or an alias, which must correctly and uniquely point to the listening address of the target server (i.e., the listen_address in the echo result of the `yasboot cluster status` command).

|Information Type |Format |Description |
|--------------------|-----------------------|---------------|
| URL | @host:port |  * host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name. In YAC deployment, if [SCAN or VIP](../YashanDB Installation via CLI/YAC Deployment.md#SCAN) has been configured, it can also be the corresponding domain name or IP address.   <br/>* port: The listening port of the YashanDB server. If not adjusted during installation, the default is 1688.   |
| Without URL |  | Indicates using [UDS local connection](../../../Product Security/Connection Management/00Connection Management) to connect to the database instance corresponding to the $YASDB_DATA environment variable. |
| Alias | @alias | Indicates connection using the alias defined in $YASDB_HOME/client/yasc_service.ini; an example of alias definition: `local = 127.0.0.1:1688`. |



### Multi-Address Connection



When connecting to YashanDB with multiple nodes using the driver, you can configure multiple listening addresses (`host:port`) or multiple groups of such addresses for connection.

- Multiple listening addresses are separated by `,`, and the connection is made to the corresponding node based on the service type (serverType) configuration during connection.

- Multiple groups of listening addresses are separated by `;` and multiple listening addresses within the same group are separated by `,`. During connection, the connection is first made to the corresponding node within the group based on the serverType configuration. When all connections within the group fail, the next group is accessed in order of priority (the earlier, the higher priority).

>**Note**:
>
> - The failover efficiency of multiple groups of listener addresses is lower than that of multiple listener addresses. Grouping is mainly used to ensure that database connections are established as much as possible only through the first group of addresses. Please choose whether to group based on actual needs.
> - In YAC/Distributed Cluster Deployment, if [SCAN or VIP](../YashanDB Installation via CLI/YAC Deployment.md#SCAN) has been configured, you can directly use the HA capabilities provided by the database server, and there is **no need** to configure an additional multi-address connection.
> - When configuring multiple addresses to connect to a YAC or a distributed cluster:
>   - In the case of single-cluster deployment, the driver will regard all its instances as primary nodes.
>   - In the primary-standby cluster deployment, the driver regards all instances in the primary cluster as primary nodes and all instances in the standby cluster as standby nodes. If load balancing is required in scenarios where SCAN or VIP is not configured, you can consider configuring multiple groups of address connections and specifying primaryLoadBalance or standbyLoadBalance as needed. All instances in the primary cluster form one group, and all instances in the standby cluster form another group.


|serverType |Description | Applicable Drivers (yashan Mode) | Applicable Drivers (mysql Mode) |
|--------------------|---------------|---------------|---------------|
| primary | This is the default type and can be omitted.<br />The driver will connect to the nodes in the order of the specified listening addresses, determine the node roles by executing `SELECT * FROM DATABASE_ROLE`, and retain the connection established with the primary node for the first time. | [JDBC driver](../../../Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction/00YashanDB JDBC Driver Usage Introduction)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect)<br/>[OCI driver](../../../Development Guide/C Language Family Drivers/OCI Driver/OCI Driver Connection Configuration)<br/>[ODBC driver](../../../Development Guide/C Language Family Drivers/ODBC Driver/ODBC Driver Installation/00ODBC Driver Installation)<br/>[Go driver](../../../Development Guide/Go Driver/Go Driver Usage Introduction)<br/>[Python driver](../../../Development Guide/Python Driver/YashanDB Python Driver Usage Introduction) | [JDBC driver](../../../Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction/00YashanDB JDBC Driver Usage Introduction)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect) |
| standby | The driver will connect to the nodes in the order of the specified listening addresses, determine the node roles by executing `SELECT * FROM DATABASE_ROLE`, and retain the connection established with the standby node for the first time. | [JDBC driver](../../../Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction/00YashanDB JDBC Driver Usage Introduction)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect)<br/>[OCI driver](../../../Development Guide/C Language Family Drivers/OCI Driver/OCI Driver Connection Configuration)<br/>[ODBC driver](../../../Development Guide/C Language Family Drivers/ODBC Driver/ODBC Driver Installation/00ODBC Driver Installation) | [JDBC driver](../../../Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction/00YashanDB JDBC Driver Usage Introduction)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect) |
| loadBalance | The driver will randomly shuffle the specified listening addresses and then establish connections. It will obtain the current session count for each node and the node with the minimum session count as the target node (if there are multiple nodes with the same minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections. | [JDBC driver](../../../Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction/00YashanDB JDBC Driver Usage Introduction)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect)<br/>[OCI driver](../../../Development Guide/C Language Family Drivers/OCI Driver/OCI Driver Connection Configuration)<br/>[ODBC driver](../../../Development Guide/C Language Family Drivers/ODBC Driver/ODBC Driver Installation/00ODBC Driver Installation)<br/>[Go driver](../../../Development Guide/Go Driver/Go Driver Usage Introduction)<br/>[Python driver](../../../Development Guide/Python Driver/YashanDB Python Driver Usage Introduction) | [JDBC driver](../../../Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction/00YashanDB JDBC Driver Usage Introduction)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect) |
| primaryLoadBalance | The driver randomly shuffles the specified listening addresses and then attempts connections. It will obtain the current session count and role of each node and select the node with the minimum session count among primary nodes as the target node (if there are multiple nodes with the same  minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections. | [JDBC driver](../../../Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction/00YashanDB JDBC Driver Usage Introduction)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect)<br/>[OCI driver](../../../Development Guide/C Language Family Drivers/OCI Driver/OCI Driver Connection Configuration)<br/>[ODBC driver](../../../Development Guide/C Language Family Drivers/ODBC Driver/ODBC Driver Installation/00ODBC Driver Installation) | [JDBC driver](../../../Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction/00YashanDB JDBC Driver Usage Introduction)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect) |
| standbyLoadBalance | The driver randomly shuffles the specified listening addresses and then attempts connections. It will obtain the current session count and role of each node and select the node with the minimum session count among standby nodes as the target node (if there are multiple nodes with the same  minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections. | [JDBC driver](../../../Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction/00YashanDB JDBC Driver Usage Introduction)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect)<br/>[OCI driver](../../../Development Guide/C Language Family Drivers/OCI Driver/OCI Driver Connection Configuration)<br/>[ODBC driver](../../../Development Guide/C Language Family Drivers/ODBC Driver/ODBC Driver Installation/00ODBC Driver Installation) | [JDBC driver](../../../Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction/00YashanDB JDBC Driver Usage Introduction)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect) |



## Connecting to YashanDB (yashan Mode)

Tools for connecting to the YashanDB server (yashan mode) include the YashanDB client (*yasql*), YashanDB drivers, the Yashan Developer Center (YDC), and general third-party database management tools (such as DBeaver, Navicat), etc.
<span id="yashan" name="yashan"></span>

### Prerequisites

- Complete the [YashanDB Client Installation](../YashanDB Client Installation/00YashanDB Client Installation) and environment variable configuration. If connecting to the local database under the installation user on the server, there is no need to reinstall *yasql*.

- Ensure that the user has the [CREATE SESSION privilege](../../../Product Security/Data Access Control/Privilege and Role Management/System Privileges) (or [CONNECT role](../../../Product Security/Data Access Control/Privilege and Role Management/Roles)).

- Ensure that the IP of the device initiating the connection is not on the [blacklist](../../../Product Security/Connection Management/Managing IP Blacklist or Whitelist).

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
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

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
    YashanDB SQL Enterprise Edition Release {version_number} x86_64
    SQL> conn sales/********@192.168.1.2:1688

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL>
    ```

- **Logging Out**

    ```sql
    exit
    ```

## Connecting to YashanDB (mysql Mode)



Tools for connecting to YashanDB (mysql mode) include the YashanDB client (*yasql*), YashanDB JDBC driver, and YashanDB C driver, as well as MySQL ecosystem tools such as MySQL client, MySQL Connector/J, etc.

This section describes how to establish a connection to the database using a YashanDB client and MySQL clint.

For details on driver configurations and connection parameters, ​please ​refer to [JDBC Driver](../../../Reference Manual of mysql Mode/Quick Start/JDBC Driver Usage Examples-MySQL Driver) and [C Driver](../../../Development Guide/C Language Family Drivers/C Driver/00C Driver).

> **Note**: 
>
> In mysql mode installation, all sessions created in the database OPEN phase default to mysql mode; sessions created during the NOMOUNT or MOUNT phases remain in yashan mode.

### Using *yasql* to Connect

#### Prerequisites

- Complete the [YashanDB Client Installation](../YashanDB Client Installation/00YashanDB Client Installation) and environment variable configuration. If connecting to the local database under the installation user on the server, there is no need to reinstall *yasql*.

- Ensure that the IP of the device initiating the connection is not on the [blacklist](../../../Product Security/Connection Management/Managing IP Blacklist or Whitelist).

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

    Parameter introduction:

    - `-S`: Indicates silent login; make sure -S is the first parameter. This parameter is case-insensitive. Using this way to log in and connect to the database does not return command-line prompts, command echoes, login prompts, or version information.

    - {authentication information}, {server address}: For details, refer to [Connection Information](#yashan).

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

### Using MySQL Client to Connect

#### Usage Rules

When using MySQL ecosystem tools to connect to YashanDB (mysql mode), the following rules apply:

- The super administrator sys cannot be used for login.

- By default, the MySQL protocol listening address (i.e., mysql_addr in the yashandb.toml configuration file) is used.

#### Prerequisites

- Complete MySQL client installation and environment variable configuration.

- A regular user must be created.

- Ensure that the IP of the device initiating the connection is not on the [blacklist](../../../Product Security/Connection Management/Managing IP Blacklist or Whitelist).

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

    - **{database_name}**: Optional, specifies the default database for subsequent operations in the current session after connecting to YashanDB. If not specified, no database will be entered, and you can specify it using the [USE](../../../Reference Manual of mysql Mode/SQL Reference/SQL Statements/USE) statement after connecting.

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


