## Connection Information

The connection information of the YashanDB server mainly includes authentication information and the server address. If using a driver for connection, multi-address connection may also be involved.

### Authentication Information

Authentication information refers to the username and credentials used to log in to YashanDB.

|Format |Description |
|--------------------|-----------------------|
| username/password <br> / | The username and password for connecting and logging into the database. When logging in via [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication), these can be omitted (i.e., no username or password is required), but the `/` cannot be omitted. <br/>If the username is not enclosed in double quotes when created in yashan mode, the connection via *yasql* is case-insensitive.<br/>If the username or password contains special characters related to OS commands (e.g., `@`, `/`, `.`, `!`, `$`, `'`, etc.), they must be escaped. |
| as role_name | Declares the role to be used when the user logs in. Available options include system administrator `SYSDBA`, administrator `DBA`, security administrator `SECURITY_ADMIN`, audit administrator `AUDIT_ADMIN`, and normal user `NORMAL` — specifying `NORMAL` or omitting it has no effect. Role names are case-insensitive. <br/><br/> In the following scenarios, a role must be declared upon login and must match the user to successfully log in:  <br/>* When logging in via [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication), the role must be declared as `SYSDBA`.  <br/>* When [the separation of duties](../../../Product Security/Data Access Control/Separation of Duties) is enabled and management users are required to specify a role upon login (default: not enforced; if needed, please contact our technical support), all management users must declare their corresponding role upon login. |

When the username or password contains special characters (except underscore), the following recommendations apply:

- When specifying the corresponding value in operations such as [creating](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE USER) or [managing](../../../Development Guide/SQL Reference Manual/SQL Statements/ALTER USER) users, use double quotes to enclose the value.

- When logging into the database, to ensure that *yasql* can correctly pass the username and password to the database server, it is recommended to use [step-by-step login](#nolog). For such usernames or passwords, only double quotes are required. If logging in directly, the username or password must be appropriately rewritten:

    - If the username or password does not already contain single quotes, it can be directly enclosed in single quotes for transmission, such as `user@name`, `password$` should be escaped and rewritten as `'"user@name"'/'"password$"'`.

    - If the username or password already contains single quotes, it cannot be transmitted using single quotes, and escaping and double quotes should be used accordingly:

        - If there are both single quotes and functionality symbols in environments like Linux, such as variable substitution symbols, escape symbols, etc., like `\`, `$`, `"`, `'`, multiple levels of escaping are required, such as `user'1$name`, `pass'1\word` should be rewritten as `"\"user'1\$name\""/"\"pass'1\\word\""`.

        - If there is a single quote but no aforementioned functionality symbols, double quotes must be used, such as `user'name`, `password` should be rewritten as `"\"user'name\""/password`.
<span id="URL" name="URL"></span>


### Server Address

The server address can be a URL or an alias, which must correctly and uniquely point to the listening address of the target server (i.e., the listen_address in the echo result of the `yasboot cluster status` command).

|Information Type |Format |Description |
|--------------------|-----------------------|---------------|
| URL | @host:port[/pdb_name] | * host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name. In YAC deployment, if [SCAN or IP](../YashanDB Installation via CLI/YAC Deployment.md#SCAN) has been configured, it can also be the corresponding domain name or IP address.  <br/>* port: The listening port of the YashanDB server. If not adjusted during installation, the default is 1688.  <br/>* pdb_name: Only applicable to CDBs. Specifies connecting to a specific PDB. If omitted, defaults to connecting to the CDB root.  |
| Without URL|  | Using [UDS local connection](../../../Product Security/Connection Management/00Connection Management) to onnect to the database instance corresponding to the local YASDB_DATA environment variable. In a CDB, this method can only be used to connect to the CDB root.<br> This connection method is a high-privilege operation and should be used with caution. |
| Alias | @alias | Connect to the database using a custom alias. The alias must be predefined in the $YASDB_HOME/client/yasc_service.ini file on the client device, with the format `alias = host:port[/pdb_name]`. For example, if the alias is defined as `yashan = 192.168.1.2:1688`, then `@yashan` can be directly used to connect to the database pointed to by this address. |



<span id="multiaddr" name="multiaddr"></span>

### Multi-Address Connection

When connecting to YashanDB with multiple nodes using the driver, you can configure multiple listening addresses (`host:port`) or multiple groups of such addresses for connection.

- Multiple listening addresses are separated by `,`, and the connection is made to the corresponding node based on the service type (serverType) configuration during connection.

- Multiple groups of listening addresses are separated by `;` and multiple listening addresses within the same group are separated by `,`. During connection, the connection is first made to the corresponding node within the group based on the serverType configuration. When all connections within the group fail, the next group is accessed in order of priority (the earlier, the higher priority).

>**Note**:
>
> - The failover efficiency of multiple groups of listener addresses is lower than that of multiple listener addresses. Grouping is mainly used to ensure that database connections are established as much as possible only through the first group of addresses. Please choose whether to group based on actual needs.
> - In YAC/Distributed Cluster Deployment, if [SCAN](../../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../../Database Administration/Cluster Management/VIP Management) has been configured, you can directly use the HA capabilities provided by the database server, and there is **no need** to configure an additional multi-address connection.
> - When configuring multiple addresses to connect to a YAC or a distributed cluster:
>   - In the case of single-cluster deployment, the driver will regard all its instances as primary nodes.
>   - In the case of primary-standby cluster deployment, the driver regards all instances in the primary cluster as primary nodes and all instances in the standby cluster as standby nodes. If load balancing is required in scenarios where SCAN or VIP is not configured, you can consider configuring multiple groups of address connections and specifying primaryLoadBalance or standbyLoadBalance as needed. All instances in the primary cluster form one group, and all instances in the standby cluster form another group.


|serverType |Description | Applicable Drivers (yashan Mode) | Applicable Drivers (mysql Mode) |
|--------------------|---------------|---------------|---------------|
| primary | This is the default type and can be omitted.<br />The driver will connect to the nodes in the order of the specified listening addresses, determine the node roles by executing `SELECT * FROM DATABASE_ROLE`, and retain the connection established with the primary node for the first time. | [JDBC driver](../../../Development Guide/JDBC Driver/00JDBC Driver)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect)<br/>[OCI driver](../../../Development Guide/C Language Family Drivers/OCI Driver/OCI Driver Connection Configuration)<br/>[ODBC driver](../../../Development Guide/C Language Family Drivers/ODBC Driver/ODBC Driver Installation/00ODBC Driver Installation)<br/>[Go driver](../../../Development Guide/Go Driver/Go Driver Usage Introduction)<br/>[Python driver](../../../Development Guide/Python Driver/YashanDB Python Driver Usage Introduction) | [JDBC driver](../../../Development Guide/JDBC Driver/00JDBC Driver)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect) |
| standby | The driver will connect to the nodes in the order of the specified listening addresses, determine the node roles by executing `SELECT * FROM DATABASE_ROLE`, and retain the connection established with the standby node for the first time. | [JDBC driver](../../../Development Guide/JDBC Driver/00JDBC Driver)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect)<br/>[OCI driver](../../../Development Guide/C Language Family Drivers/OCI Driver/OCI Driver Connection Configuration)<br/>[ODBC driver](../../../Development Guide/C Language Family Drivers/ODBC Driver/ODBC Driver Installation/00ODBC Driver Installation) | [JDBC driver](../../../Development Guide/JDBC Driver/00JDBC Driver)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect) |
| loadBalance | The driver will randomly shuffle the specified listening addresses and then establish connections. It will obtain the current session count for each node and the node with the minimum session count as the target node (if there are multiple nodes with the same minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections.  | [JDBC driver](../../../Development Guide/JDBC Driver/00JDBC Driver)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect)<br/>[OCI driver](../../../Development Guide/C Language Family Drivers/OCI Driver/OCI Driver Connection Configuration)<br/>[ODBC driver](../../../Development Guide/C Language Family Drivers/ODBC Driver/ODBC Driver Installation/00ODBC Driver Installation)<br/>[Go driver](../../../Development Guide/Go Driver/Go Driver Usage Introduction)<br/>[Python driver](../../../Development Guide/Python Driver/YashanDB Python Driver Usage Introduction) | [JDBC driver](../../../Development Guide/JDBC Driver/00JDBC Driver)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect) |
| primaryLoadBalance | The driver randomly shuffles the specified listening addresses and then attempts connections. It will obtain the current session count and role of each node and select the node with the minimum session count among primary nodes as the target node (if there are multiple nodes with the same  minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections. | [JDBC driver](../../../Development Guide/JDBC Driver/00JDBC Driver)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect)<br/>[OCI driver](../../../Development Guide/C Language Family Drivers/OCI Driver/OCI Driver Connection Configuration)<br/>[ODBC driver](../../../Development Guide/C Language Family Drivers/ODBC Driver/ODBC Driver Installation/00ODBC Driver Installation) | [JDBC driver](../../../Development Guide/JDBC Driver/00JDBC Driver)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect) |
| standbyLoadBalance | The driver randomly shuffles the specified listening addresses and then attempts connections. It will obtain the current session count and role of each node and select the node with the minimum session count among standby nodes as the target node (if there are multiple nodes with the same  minimum count, the first node to establish connection is selected). It will retain the connection to the target node and close the other connections. | [JDBC driver](../../../Development Guide/JDBC Driver/00JDBC Driver)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect)<br/>[OCI driver](../../../Development Guide/C Language Family Drivers/OCI Driver/OCI Driver Connection Configuration)<br/>[ODBC driver](../../../Development Guide/C Language Family Drivers/ODBC Driver/ODBC Driver Installation/00ODBC Driver Installation) | [JDBC driver](../../../Development Guide/JDBC Driver/00JDBC Driver)<br/>[C driver](../../../Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect) |

## Connecting to YashanDB (yashan Mode)

Tools for connecting to the YashanDB server (yashan mode) include the YashanDB client (*yasql*), YashanDB drivers, the Yashan Developer Center (YDC), and general third-party database management tools (such as DBeaver, Navicat), etc.
<span id="yashan" name="yashan"></span>

### Prerequisites

- In CDBs, when specifying pdb_name to connect to a specific PDB, clients of the matching version v23.5.1.100 or higher must be used. 

- Complete the [YashanDB Client Installation](../../Installation and Deployment/YashanDB Client Installation/00YashanDB Client Installation) and environment variable configuration. If connecting to the local database under the installation user on the server, there is no need to reinstall *yasql*.

- Ensure that the user has the [CREATE SESSION privilege](../../../Product Security/Data Access Control/Privilege and Role Management/System Privileges) (or [CONNECT role](../../../Product Security/Data Access Control/Privilege and Role Management/Roles)).

- Ensure that the IP of the device initiating the connection is not on the [blacklist](../../../Product Security/Connection Management/Managing IP Blacklist or Whitelist).

### Connection Operations

This document only covers how to connect to the YashanDB server (yashan mode) using the YashanDB client (*yasql*). Connection methods for YashanDB drivers/toolkits will be detailed in corresponding documents.

####  One-Step Login

Command format is as follows:

```shell
# One-Step Login
$ yasql [-S] {authentication information}{server address} [login role]
# Common usages are as follows:
$ yasql [-S] username/password@host:port[/pdb_name]
$ yasql [-S] / as sysdba
```

`-S` means silent login, making sure `-S` is the first parameter. This parameter is case-insensitive. Using this way to log in and connect to the database does not return command-line prompts, command echoes, login prompts, or version information.

Example of logging in with the sys user: (if the current environment is a CDB, it means logging into the CDB root)  

```shell
# OS authentication (for local login)
$ yasql / as sysdba

# Password authentication
$ yasql sys/********@192.168.1.2:1688

YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

SQL> 
```

Example of logging in PDB1 with the sales user:
```shell
$ yasql sales/********@192.168.1.2:1688/pdb1
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

SQL> 
```
The appearance of the SQL command line prompt indicates a successful connection; if silent login, the SQL prompt will not appear.

<span id="nolog" name="nolog"></span>

####  Step-by-Step Login

Command format is as follows:

```shell
# 1. Start yasql, open the login window
$ yasql /nolog

# 2. Use the connect or conn command to connect and log in to the database
SQL> conn {authentication information}{server address}
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

###  Logging Out

Command format is as follows:

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
    $ yasql [-S] username/password@host:port[/pdb_name]  
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

    - **-P {port}**: The listening port for the MySQL protocol on the YashanDB server (i.e., the port section of mysql_addr in the yashandb.toml configuration file). If not adjusted during installation, it defaults to 1690 (Standalone (primary/standby) deployment) or 1691 (YAC deployment).

    - **-u {username}**: The database username.

    - **-p{password}**: The password corresponding to the username.

    - **{database_name}**: Optional, specifies the default database for subsequent operations in the current session after connecting to YashanDB. If not specified, no database will be entered, and you can specify it using the [USE](../../../Reference Manual of mysql Mode/SQL Reference/SQL Statements/USE) statement after connecting.

    Example of logging in with the sales user:

    ```shell
    -- the default port is 1690 (Standalone deployment) or 1691 (YAC deployment)
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


