## Connection Information

The connection information of PDB mainly includes authentication information and the server address. If using a driver for connection, multi-address connection may also be involved.

### Authentication Information

Authentication information refers to the username and credentials used to log in to PDB.

|Format |Description |
|--------------------|-----------------------|
| username/password| Username and password for connecting and logging into the database.<br/>If the username is not enclosed in double quotes when created in yashan mode, the connection via *yasql* is case-insensitive.<br/>If the username or password contains special characters related to OS commands (e.g., `@`, `/`, `.`, `!`, `$`, `'`, etc.), they must be escaped. For details, please refer to [yasql Usage Guide](../../../Tools Guide/yasql/User Guide for yasql). |

### Server Address

The server address can be a URL or alias that correctly and uniquely points to the target PDB.

|Information Type |Format |Description |
|--------------------|-----------------------|---------------|
| URL | @host:port[/pdb_name] |  * host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name. In YAC deployment, if [SCAN](../../Cluster Management/SCAN Management) or [VIP](../../Cluster Management/VIP Management) has been configured, it can also be the corresponding domain name or IP address.  <br/>* port: The listening port of the YashanDB server. If not adjusted during installation, the default is 1688.  <br/>* pdb_name: Specifies connecting to a specific PDB. If omitted, defaults to connecting to the CDB root.  |
| Alias | @alias | Connect to PDB using a custom alias. The alias must be predefined in the $YASDB_HOME/client/yasc_service.ini file on the client device, with the format `alias = host:port[/pdb_name]`. For example, if the alias is defined as `yashanPDB1 = 192.168.1.2:1688/pdb1`, then `@yashanPDB1` can be directly used to connect to the database pointed to by this address. |


### Multi-Address Connection

When using a driver to connect to a PDB with multiple nodes, you can configure multiple or multiple groups of listening addresses (`host:port`) for connection. The format is `serverType:host:port,host:port,host:port/pdb_name`, and the rest is the same as [Connecting to YashanDB with multiple addresses](../../../Installation and Upgrade/Installation and Deployment/Initial Environment after Installation/Connecting to YashanDB.md#multiaddr).

## Connecting to PDB

Tools for connecting to PDB include the YashanDB client (*yasql*), YashanDB drivers, the Yashan Developer Center (YDC), etc.

### Prerequisites

- When connecting to a PDB, you need to use the supporting YashanDB client (yasql), YashanDB driver, etc. of version v23.5.1.100 or above.

- Compatibility of PDB (mysql mode) with tools in the MySQL ecosystem has not been verified yet. It is recommended to use tools such as the YashanDB client and YashanDB driver for connection.

- The corresponding versions of the YashanDB client and YashanDB driver have been installed, and the environment variables have been configured. If you are connecting to the local database on the database server, there is no need to reinstall *yasql*.

- Ensure that the user has the [CREATE SESSION privilege](../../../Product Security/Data Access Control/Privilege and Role Management/System Privileges) (or [CONNECT role](../../../Product Security/Data Access Control/Privilege and Role Management/Roles)).

- Ensure that the IP of the device initiating the connection is not on the [blacklist](../../../Product Security/Connection Management/Managing IP Blacklist or Whitelist).

### Connection Operations

This document only covers how to connect to PDB using the YashanDB client (*yasql*). Connection methods for YashanDB drivers/toolkits will be detailed in corresponding documents.

####  One-Step Login

Command format is as follows:

```shell
# One-Step Login
$ yasql [-S] {authentication information}{server address}  
# Common usages are as follows:
$ yasql [-S] username/password@host:port[/pdb_name]
$ yasql [-S] / as sysdba
```

`-S` means silent login, making sure `-S` is the first parameter. This parameter is case-insensitive. Using this way to log in and connect to the database does not return command-line prompts, command echoes, login prompts, or version information.

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
SQL> conn {authentication information}{server address}
```

Example of logging in PDB1 with the sales user:

```shell
$ yasql /nolog
YashanDB SQL Enterprise Edition Release {version_number} x86_64
SQL> conn sales/********@192.168.1.2:1688/pdb1

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

SQL>
```

###  Logging Out

Command format is as follows:

```sql
exit
```
