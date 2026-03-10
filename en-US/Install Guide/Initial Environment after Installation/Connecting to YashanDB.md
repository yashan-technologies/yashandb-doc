## yashan Mode



Tools for connecting to the YashanDB server (yashan mode) include the YashanDB client (*yasql*), YashanDB drivers, the Yashan Developer Center (YDC), and general third-party database management tools (such as DBeaver, Navicat), etc.
<span id="yashan" name="yashan" class="yaslink"></span>

### Connection Information



The connection information for the YashanDB server mainly includes authentication information and the server address. If connecting via a driver, the connection mode for multiple IP scenarios is also involved.



#### Authentication Information

Authentication information refers to the username and credentials used to log in to YashanDB.

|Format |Description |
|--------------------|-----------------------|
| username/password| Username and password for connecting and logging into the database.<br/>If the username is not enclosed in double quotes when created in yashan mode, the connection via *yasql* is case-insensitive.<br/>If the username or password contains special characters related to OS commands (e.g., `@`, `/`, `.`, `!`, `$`, `'`, etc.), they must be escaped. For details, please refer to [yasql Usage Guide](../../All Manuals/Tools Guide/yasql/User Guide for yasql). |
| / as sysdba| When logging in through [Operating System Authentication](../../All Manuals/Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) on the server where the database is hosted, the username and password can be omitted, but the `/` cannot be omitted, and `as sysdba` must be declared. |




#### Server Address

The server address can be a URL or an alias, which must correctly point to the listening address of the target server (i.e., LISTEN_ADDR in the yashandb.toml configuration file).

|Information Type |Format |Description |
|--------------------|-----------------------|---------------|
| URL | @host:port/pdb_name | <br/>* host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name.<br/>* port: The listening port of the YashanDB server. If not adjusted during installation, the default is 1688.<br/>* pdb_name: Only applicable to CDBs. Specifies connecting to a specific PDB. If omitted, defaults to connecting to the CDB root. |
| Without URL|  | Using [UDS local connection](../../All Manuals/Product Security/Connection Management/00Connection Management) to onnect to the database instance corresponding to the local YASDB_DATA environment variable. This connection method is a high-privilege operation and should be used with caution. |
| Alias | @alias | Connect to the database using a custom alias. The alias must be predefined in the $YASDB_HOME/client/yasc_service.ini file on the client device, with the format `alias = host:port[/pdb_name]`. For example, if the alias is defined as `yashan = 192.168.1.2:1688`, then @yashan can be directly used to connect to the database pointed to by this address. |




#### Connection Modes (Multi-IP Scenarios)

Connection modes (multi-IP scenarios) can only be configured when connecting to the YashanDB server via a driver, such as the [JDBC driver](../../All Manuals/Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction), [C driver](../../All Manuals/Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect), [OCI driver](../../All Manuals/Development Guide/C Language Family Drivers/OCI Driver/OCI Driver Connection Configuration), and [ODBC driver](../../All Manuals/Development Guide/C Language Family Drivers/ODBC Driver/ODBC Driver Installation/00ODBC Driver Installation), and requires multiple nodes on the YashanDB server.



|Connection Mode |Format |Description |
|--------------------|-----------------------|---------------|
| primary mode | PRIMARY:host1:port,host2:port,…,hostn:port<br/>(`PRIMARY:` can be omitted) | Polling to connect to the primary database node in a high-availability deployment |
| standby mode |STANDBY:host1:port,host2:port,…,hostn:port | Polling to connect to the standby database node in a high-availability deployment |
| loadBalance mode | LOADBALANCE:host1:port,host2:port,…,hostn:port| Connecting to the optimal node (the node with the least connections is considered optimal) |
| primaryLoadBalance mode| PRIMARYLOADBALANCE:host1:port,host2:port,…,hostn:port | Connecting to the optimal primary database node |
| standbyLoadBalance mode| STANDBYLOADBALANCE:host1:port,host2:port,…,hostn:port | Load balancing to connect to the optimal standby database node |



### Prerequisites

- In CDBs, when specifying pdb_name to connect to a specific PDB, clients of the matching version v23.5.1.100 or higher must be used. 

- Complete the [YashanDB Client Installation](../YashanDB Client Installation/00YashanDB Client Installation) and environment variable configuration. If connecting to the local database under the installation user on the server, there is no need to reinstall *yasql*.

- Ensure that the user has the [CREATE SESSION privilege](../../All Manuals/Product Security/Data Access Control/Privilege and Role Management/System Privileges) (or [CONNECT role](../../All Manuals/Product Security/Data Access Control/Privilege and Role Management/Roles)).

- Ensure that the IP of the device initiating the connection is not on the [blacklist](../../All Manuals/Product Security/Connection Management/IP Blacklist and Whitelist Management).

### Connection Operations

This document only covers how to connect to the YashanDB server (yashan mode) using the YashanDB client (*yasql*). Connection methods for YashanDB drivers/toolkits will be detailed in corresponding documents.

####  One-Step Login

Command format is as follows:

```shell
# One-Step Login
$ yasql [-S] {authentication information} {server address}  
# Common usages are as follows:
$ yasql [-S] username/password@host:port/pdb_name
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

####  Step-by-Step Login

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

####  Logging Out

Command format is as follows:

```sql
exit
```



## mysql Mode


Tools for connecting to YashanDB (mysql mode) include the YashanDB client (*yasql*), YashanDB JDBC driver, and YashanDB C driver, as well as MySQL ecosystem tools such as MySQL client, MySQL Connector/J, etc.

This section describes how to establish a connection to the database using a YashanDB client and MySQL clint.

For details on driver configurations and connection parameters, ​please ​refer to [JDBC Driver](../../All Manuals/Reference Manual of mysql Mode/Quick Start/JDBC Driver Usage Examples-MySQL Driver) and [C Driver](../../All Manuals/Development Guide/C Language Family Drivers/C Driver/00C Driver).

> **Note**: 
>
> In mysql mode installation, all sessions created in the database OPEN phase default to mysql mode; sessions created during the NOMOUNT or MOUNT phases remain in yashan mode.

### Using *yasql* to Connect

#### Connection Information



The connection information for the YashanDB server mainly includes authentication information and the server address. If connecting via a driver, the connection mode for multiple IP scenarios is also involved.



#### Authentication Information

Authentication information refers to the username and credentials used to log in to YashanDB.

|Format |Description |
|--------------------|-----------------------|
| username/password| Username and password for connecting and logging into the database.<br/>If the username is not enclosed in double quotes when created in yashan mode, the connection via *yasql* is case-insensitive.<br/>If the username or password contains special characters related to OS commands (e.g., `@`, `/`, `.`, `!`, `$`, `'`, etc.), they must be escaped. For details, please refer to [yasql Usage Guide](../../All Manuals/Tools Guide/yasql/User Guide for yasql). |
| / as sysdba| When logging in through [Operating System Authentication](../../All Manuals/Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) on the server where the database is hosted, the username and password can be omitted, but the `/` cannot be omitted, and `as sysdba` must be declared. |




#### Server Address

The server address can be a URL or an alias, which must correctly point to the listening address of the target server (i.e., LISTEN_ADDR in the yashandb.toml configuration file).

|Information Type |Format |Description |
|--------------------|-----------------------|---------------|
| URL | @host:port/pdb_name | <br/>* host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name.<br/>* port: The listening port of the YashanDB server. If not adjusted during installation, the default is 1688.<br/>* pdb_name: Only applicable to CDBs. Specifies connecting to a specific PDB. If omitted, defaults to connecting to the CDB root. |
| Without URL|  | Using [UDS local connection](../../All Manuals/Product Security/Connection Management/00Connection Management) to onnect to the database instance corresponding to the local YASDB_DATA environment variable. This connection method is a high-privilege operation and should be used with caution. |
| Alias | @alias | Connect to the database using a custom alias. The alias must be predefined in the $YASDB_HOME/client/yasc_service.ini file on the client device, with the format `alias = host:port[/pdb_name]`. For example, if the alias is defined as `yashan = 192.168.1.2:1688`, then @yashan can be directly used to connect to the database pointed to by this address. |




The connection mode (multi-IP scenario) can only be configured when using the driver to connect to the YashanDB server. The mysql mode supports only the [JDBC Driver](../../All Manuals/Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction) and [C Driver](../../All Manuals/Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect) , and requires that the YashanDB server has multiple nodes.



|Connection Mode |Format |Description |
|--------------------|-----------------------|---------------|
| primary mode | PRIMARY:host1:port,host2:port,…,hostn:port<br/>(`PRIMARY:` can be omitted) | Polling to connect to the primary database node in a high-availability deployment |
| standby mode |STANDBY:host1:port,host2:port,…,hostn:port | Polling to connect to the standby database node in a high-availability deployment |
| loadBalance mode | LOADBALANCE:host1:port,host2:port,…,hostn:port| Connecting to the optimal node (the node with the least connections is considered optimal) |
| primaryLoadBalance mode| PRIMARYLOADBALANCE:host1:port,host2:port,…,hostn:port | Connecting to the optimal primary database node |
| standbyLoadBalance mode| STANDBYLOADBALANCE:host1:port,host2:port,…,hostn:port | Load balancing to connect to the optimal standby database node |



#### Prerequisites

- Complete the [YashanDB Client Installation](../YashanDB Client Installation/00YashanDB Client Installation) and environment variable configuration. If connecting to the local database under the installation user on the server, there is no need to reinstall *yasql*.

- Ensure that the IP of the device initiating the connection is not on the [blacklist](../../All Manuals/Product Security/Connection Management/IP Blacklist and Whitelist Management).

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

When connecting to YashanDB (mysql mode) using the MySQL client, logging in as the super administrator sys is not allowed.

#### Connection Information

- Authentication Information: Same as [yashan mode](#yashan).

- Server Address: When connecting to YashanDB using MySQL ecosystem tools, the default listening address for MySQL protocol (i.e., mysql_addr in the yashandb.toml configuration file) is used.

#### Prerequisites

- Complete MySQL client installation and environment variable configuration.

- A regular user must be created.

- Ensure that the IP of the device initiating the connection is not on the [blacklist](../../All Manuals/Product Security/Connection Management/IP Blacklist and Whitelist Management).

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

    - **{database_name}**: Optional, specifies the default database for subsequent operations in the current session after connecting to YashanDB. If not specified, no database will be entered, and you can specify it using the [USE](../../All Manuals/Reference Manual of mysql Mode/SQL Reference/SQL Statements/USE) statement after connecting.

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

