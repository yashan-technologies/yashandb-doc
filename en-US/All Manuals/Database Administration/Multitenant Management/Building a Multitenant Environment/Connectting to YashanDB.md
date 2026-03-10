

Tools for connecting to the YashanDB server (yashan mode) include the YashanDB client (*yasql*), YashanDB drivers, the Yashan Developer Center (YDC), and general third-party database management tools (such as DBeaver, Navicat), etc.
<span id="yashan" name="yashan" class="yaslink"></span>

## Connection Information



The connection information for the YashanDB server mainly includes authentication information and the server address. If connecting via a driver, the connection mode for multiple IP scenarios is also involved.



### Authentication Information

Authentication information refers to the username and credentials used to log in to YashanDB.

|Format |Description |
|--------------------|-----------------------|
| username/password| Username and password for connecting and logging into the database.<br/>If the username is not enclosed in double quotes when created in yashan mode, the connection via *yasql* is case-insensitive.<br/>If the username or password contains special characters related to OS commands (e.g., `@`, `/`, `.`, `!`, `$`, `'`, etc.), they must be escaped. For details, please refer to [yasql Usage Guide](../../../Tools Guide/yasql/User Guide for yasql). |
| / as sysdba| When logging in through [Operating System Authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) on the server where the database is hosted, the username and password can be omitted, but the `/` cannot be omitted, and `as sysdba` must be declared. |




### Server Address

The server address can be a URL or an alias, which must correctly point to the listening address of the target server (i.e., LISTEN_ADDR in the yashandb.toml configuration file).

|Information Type |Format |Description |
|--------------------|-----------------------|---------------|
| URL | @host:port/pdb_name | <br/>* host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name.<br/>* port: The listening port of the YashanDB server. If not adjusted during installation, the default is 1688.<br/>* pdb_name: Only applicable to CDBs. Specifies connecting to a specific PDB. If omitted, defaults to connecting to the CDB root. |
| Without URL|  | Using [UDS local connection](../../../Product Security/Connection Management/00Connection Management) to onnect to the database instance corresponding to the local YASDB_DATA environment variable. This connection method is a high-privilege operation and should be used with caution. |
| Alias | @alias | Connect to the database using a custom alias. The alias must be predefined in the $YASDB_HOME/client/yasc_service.ini file on the client device, with the format `alias = host:port[/pdb_name]`. For example, if the alias is defined as `yashan = 192.168.1.2:1688`, then @yashan can be directly used to connect to the database pointed to by this address. |




### Connection Modes (Multi-IP Scenarios)

Connection modes (multi-IP scenarios) can only be configured when connecting to the YashanDB server via a driver, such as the [JDBC driver](../../../Development Guide/JDBC Driver/YashanDB JDBC Driver Usage Introduction), [C driver](../../../Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect), [OCI driver](../../../Development Guide/C Language Family Drivers/OCI Driver/OCI Driver Connection Configuration), and [ODBC driver](../../../Development Guide/C Language Family Drivers/ODBC Driver/ODBC Driver Installation/00ODBC Driver Installation), and requires multiple nodes on the YashanDB server.



|Connection Mode |Format |Description |
|--------------------|-----------------------|---------------|
| primary mode | PRIMARY:host1:port,host2:port,…,hostn:port<br/>(`PRIMARY:` can be omitted) | Polling to connect to the primary database node in a high-availability deployment |
| standby mode |STANDBY:host1:port,host2:port,…,hostn:port | Polling to connect to the standby database node in a high-availability deployment |
| loadBalance mode | LOADBALANCE:host1:port,host2:port,…,hostn:port| Connecting to the optimal node (the node with the least connections is considered optimal) |
| primaryLoadBalance mode| PRIMARYLOADBALANCE:host1:port,host2:port,…,hostn:port | Connecting to the optimal primary database node |
| standbyLoadBalance mode| STANDBYLOADBALANCE:host1:port,host2:port,…,hostn:port | Load balancing to connect to the optimal standby database node |



## Prerequisites

- In CDBs, when specifying pdb_name to connect to a specific PDB, clients of the matching version v23.5.1.100 or higher must be used. 

- Complete the [YashanDB Client Installation](../../../Installation and Upgrade/Installation and Deployment/YashanDB Client Installation/00YashanDB Client Installation) and environment variable configuration. If connecting to the local database under the installation user on the server, there is no need to reinstall *yasql*.

- Ensure that the user has the [CREATE SESSION privilege](../../../Product Security/Data Access Control/Privilege and Role Management/System Privileges) (or [CONNECT role](../../../Product Security/Data Access Control/Privilege and Role Management/Roles)).

- Ensure that the IP of the device initiating the connection is not on the [blacklist](../../../Product Security/Connection Management/IP Blacklist and Whitelist Management).

## Connection Operations

This document only covers how to connect to the YashanDB server (yashan mode) using the YashanDB client (*yasql*). Connection methods for YashanDB drivers/toolkits will be detailed in corresponding documents.

###  One-Step Login

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

###  Step-by-Step Login

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

###  Logging Out

Command format is as follows:

```sql
exit
```

