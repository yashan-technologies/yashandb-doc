## View Help and Version Information

Use the `yasql -H` command to view help and version information.

```shell
$ yasql -H
```

Use the `yasql -V` command to view version information.

```shell
$ yasql -V
```

## Command Format

```shell
$ yasql [-S] username/password@host:port[/pdb_name] [as role_name] [-c "SQL" | -f [-e]  filename[.ext] | @filename[.ext]] 
SQL> <yasql_subcommand>
```

**-S**

Specify [silent login](#login). Ensure that `-S` is the first parameter, and this parameter is case insensitive.

**username/password** or **/**

Specify the database username and password for [logging in or connecting](#login) to the database.

**@host:port[/pdb_name]**

Specify the target database address for [login or connection](#login), where `/pdb_name` is used in a CDB to connect to a specific PDB (if omitted, defaults to connecting to the CDB root).  

<span id="role_name" name="role_name"></span>

**as role_name**

Declare the role to be used when the user [logs in](#login). Available options include system administrator `SYSDBA`, administrator `DBA`, audit administrator `AUDIT_ADMIN`, security administrator `SECURITY_ADMIN`, and normal user `NORMAL` — specifying `NORMAL` or omitting it has no effect. Role names are case-insensitive.

**-c "SQL"**

Used to log in and execute a single SQL statement (and exit immediately after execution); for details, please refer to [executing SQL statements](#singlesql).

**-f [-e] filename[.ext]**

Used to execute SQL files; for details, please refer to [executing SQL files](#fsql).

**@filename[.ext]**

Used to execute SQL files; for details, please refer to [executing SQL statements](#sqlfile).

**yasql_subcommand**

After connecting and logging into the database via *yasql*, the available subcommands (case-insensitive) are listed in the table below.

|Subcommand |Function  |
| ------------ | ------------------------------------------ |
| [@filename](#sqlfile)<br />[@@filename](#sqlfile) | Execute SQL files     |
| [!{OS_command}](#shell) | Execute OS-level shell commands   |
| [set lastlogin on](#lastlogin) | Set whether to display the last login record of this user |
| [show](#show) | View parameter configuration   |
| [desc](#desc)  | Retrieve description information for database objects, such as column definitions for tables or views |
| [set DIRECTEXECUTE](#directexecute) | Set whether to enable direct execution of SQL statements |
| [set AUTOCOMMIT](#autocommit) | Set whether to enable automatic commit for transactions |
| [set TIMING](#timing) | Set whether to enable execution time statistics  |
| [set FEEDBACK](#feedback) | Set whether to display feedback information, such as "ROW_NUM row/rows affected." |
| [set SERVEROUTPUT](#serveroutput) | Set whether to print information from the server-side buffer configured by [DBMS_OUTPUT](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_OUTPUT).PUT_LINE |
| [spool](#spool) | Save the output of an SQL statement to a file   |
| [set AUTOTRACE](#autotrace) | Set whether to generate execution plan analysis reports for DML statements (SELECT/INSERT/UPDATE/DELETE) |
| [set nchar_literal_replace](#nchar) | Set whether to enable NCHAR literal replacement |
| [set DEFINE](#set_def) | Set whether to enable scanning and replacing command placeholders "&" variables |
| [DEFINE](#DEFINE) | Assign values to variables (CHAR values) |
| [set NUMWIDTH](#numwidth) | Set the display width for floating-point and NUMBER type data output |
| [set HEADING](#heading) | Set whether to print column headers in query results |
| [COLUMN](#COLUMN) | Manage column display attributes |
| [EXIT](#EXIT) | Log out |

## Functionality Introduction

<span id="login" name="login"></span>

### Login and Connection

To log in to YashanDB, you first need to obtain the database's authentication information and connection information, then select an appropriate login method to perform the operation.

#### Authentication Information

Authentication information refers to the username and credentials used to log in to YashanDB.

|Format |Description |
|--------------------|-----------------------|
| username/password <br> / | The username and password for connecting and logging into the database. When logging in via [OS authentication](../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication), these can be omitted (i.e., no username or password is required), but the `/` cannot be omitted. <br/>If the username is not enclosed in double quotes when created in yashan mode, the connection via *yasql* is case-insensitive.<br/>If the username or password contains special characters related to OS commands (e.g., `@`, `/`, `.`, `!`, `$`, `'`, etc.), they must be escaped. |
| as role_name | Declares the role to be used when the user logs in. Available options include system administrator `SYSDBA`, administrator `DBA`, security administrator `SECURITY_ADMIN`, audit administrator `AUDIT_ADMIN`, and normal user `NORMAL` — specifying `NORMAL` or omitting it has no effect. Role names are case-insensitive. <br/><br/> In the following scenarios, a role must be declared upon login and must match the user to successfully log in:  <br/>* When logging in via [OS authentication](../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication), the role must be declared as `SYSDBA`.  <br/>* When [the separation of duties](../../Product Security/Data Access Control/Separation of Duties) is enabled and management users are required to specify a role upon login (default: not enforced; if needed, please contact our technical support), all management users must declare their corresponding role upon login. |

When the username or password contains special characters (except underscore), the following recommendations apply:

- When specifying the corresponding value in operations such as [creating](../../Development Guide/SQL Reference Manual/SQL Statements/CREATE USER) or [managing](../../Development Guide/SQL Reference Manual/SQL Statements/ALTER USER) users, use double quotes to enclose the value.

- When logging into the database, to ensure that *yasql* can correctly pass the username and password to the database server, it is recommended to use [step-by-step login](#nolog). For such usernames or passwords, only double quotes are required. If logging in directly, the username or password must be appropriately rewritten:

    - If the username or password does not already contain single quotes, it can be directly enclosed in single quotes for transmission, such as `user@name`, `password$` should be escaped and rewritten as `'"user@name"'/'"password$"'`.

    - If the username or password already contains single quotes, it cannot be transmitted using single quotes, and escaping and double quotes should be used accordingly:

        - If there are both single quotes and functionality symbols in environments like Linux, such as variable substitution symbols, escape symbols, etc., like `\`, `$`, `"`, `'`, multiple levels of escaping are required, such as `user'1$name`, `pass'1\word` should be rewritten as `"\"user'1\$name\""/"\"pass'1\\word\""`.

        - If there is a single quote but no aforementioned functionality symbols, double quotes must be used, such as `user'name`, `password` should be rewritten as `"\"user'name\""/password`.

> **Note**: 
>
> To correctly pass the username to the database server, users created using *yasql* in mysql mode connecting to YashanDB (mysql mode) need to be additionally enclosed in double quotes.

### Server Address

The server address can be a URL or an alias, which must correctly and uniquely point to the listening address of the target server (i.e., the listen_address in the echo result of the `yasboot cluster status` command).

|Information Type |Format |Description |
|--------------------|-----------------------|---------------|
| URL | @host:port[/pdb_name] | * host: The network address of the server where the database resides, which can be an IPv4 address, an IPv6 address, or a domain name. In YAC deployment, if [SCAN or IP](../../Database Administration/Cluster Management/SCAN Management) has been configured, it can also be the corresponding domain name or IP address.  <br/>* port: The listening port of the YashanDB server. If not adjusted during installation, the default is 1688. <br/>* pdb_name: Only applicable to CDBs. Specifies connecting to a specific PDB. If omitted, defaults to connecting to the CDB root.  |
| Without URL|  | Using [UDS local connection](../../Product Security/Connection Management/00Connection Management) to onnect to the database instance corresponding to the local YASDB_DATA environment variable. In a CDB, this method can only be used to connect to the CDB root.<br> This connection method is a high-privilege operation and should be used with caution. |
| Alias | @alias | Connect to the database using a custom alias. The alias must be predefined in the $YASDB_HOME/client/yasc_service.ini file on the client device, with the format `alias = host:port[/pdb_name]`. For example, if the alias is defined as `yashan = 192.168.1.2:1688`, then `@yashan` can be directly used to connect to the database pointed to by this address. |

- In CDBs, when specifying pdb_name to connect to a specific PDB, *yasql* of the matching version v23.5.1.100 or higher must be used.

- When using an IPv6 address for connections, the IP must be enclosed in square brackets. Link-local IPv6 addresses additionally require a percent sign (%) followed by the network interface name. The system network card must support IPv6 connectivity. Currently supported protocols include:

  - IPv6 loopback address, for example, `@[::1]:1688`

  - Link-local address, for example, `@[fe80::a89b:8fdb:2c28:35ce%ens192]:1688`

  - Unique local address, for example, `@[fc00:7::126]:1688`

  - Unspecified address, for example, `@[::]:1688`

  - Global unicast address, for example, `@[2001:250:4000:4000:f80f:6d4c:4310:35ce]:1688`

  - IPv4-mapped IPv6 address, for example, `@[::ffff:192.168.7.126]:1688`

- When using a domain name for connection, e.g., `@yashandb.vip:1688`, the domain name must correctly resolve to the actual database IP address. If the domain name resolves to multiple IP addresses, the system will attempt to connect to these IP addresses in the order of resolution until successful.  

####  Login Methods

Login methods can be divided into the following types:
<span id="nolog" name="nolog"></span>

|Login Methods |Description |
|--------------------|-----------------------|
| Direct login | Command format: `yasql username/password@host:port[/pdb_name]`  |
| Silent login| Command format: `yasql -S username/password@host:port[/pdb_name]` <br/> Using this method to log in and connect to the database will not return command prompts, command echo, login prompt information, and version information. |
| OS authentication login|  Command format: `yasql / as sysdba` or `yasql username/password as sysdba` <br/>Using this method for login does not require configuring a connection type; it can only connect to the database instance corresponding to the $YASDB_DATA environment variable via UDS local connection. |
| Step-by-step login| 1. Start *yasql* using the `/nolog` command, command format: `yasql /nolog`. At this point, only the login window opens, but the database is not connected.<br/>2. Use the `connect` or `conn` command to connect and log in to the database, command format: `connect username/password@host:port[/pdb_name]` or `conn username/password@host:port[/pdb_name]`. |
| Switching logins| Using the `connect` or `conn` command to switch to another user after logging in. |

####  Login Examples

```shell
# Direct login + IPv4 connection with URL
$ yasql sales/********@192.168.1.2:1688
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
SQL>

# Direct login + IPv6 loopback address connection
$ yasql sales/********@[::1]:1688
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
SQL>

# Silent login + Domain name connection with URL
$ yasql -S sales/sales@123.com:1688

# Operating system authentication login
$ yasql / as sysdba
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
SQL>

# Direct login + Alias connection
$ cat ${YASDB_HOME}/client/yasc_service.ini
yashan = 127.0.0.1:1688
$ yasql sales/********@yashan
Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
SQL>

# Step-by-step login
$ yasql /nolog
YashanDB SQL Enterprise Edition Release {version_number} x86_64
SQL> conn sales/sales@192.168.1.2:1688
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
SQL>
-- Login successful

-- Switch login: switch to another user via username and password + connection without URL
SQL> conn sales1/sales1

-- Switch login: switch to another user via username and password + IPv4 connection with URL
SQL> conn sales1/sales1@192.168.1.3:1688
SQL>
```

### Execute SQL Statement

**Method 1**: Execute SQL statements after logging in

After logging in to the database, you can directly execute SQL statements in the *yasql* command-line interface.

***Example***

```shell
$ yasql sales/********@192.168.1.2:1688
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
SQL> CREATE TABLE customer(cno INT, cname VARCHAR(50));
 
Succeed.
 
SQL> INSERT INTO customer VALUES(1, 'customer1');
 
1 row affected.
 
SQL> SELECT cno,cname FROM customer;
 
         CNO CNAME                                                
------------ -----------------------------------------------------
           1 customer1                                           
 
1 row fetched.
```

<span id="singlesql" name="singlesql"></span>
**Method 2**: Execute a single SQL statement and exit

By using the `-c "SQL"` parameter, you can directly connect and log in to the database to execute a single SQL statement, and exit without retaining the login status.

***Example***

```sql
$ yasql sales/******** -c "select 1 from dual";
           1
------------
           1
```

### Execute SQL Files

<span id="fsql" name="fsql"></span>
**Method 1**: By using the `-f\[-e\]` parameter

Using the `-f \[-e\] filename[.ext]` parameter, you can directly connect and log in to the database to execute a SQL file, and the file extension can be omitted.

- `-f` specifies the SQL file to execute.
- `-e` is used to display the specific statements being executed in the file.

When the SQL file is in the same directory as the *yasql* tool, you do not need to specify the file path; otherwise, the path of the file must be specified (absolute or relative path is acceptable).

***Example***

```sql
-- s.sql is in the same directory as *yasql*
$ yasql sales/******** -f s.sql
SQL> select 1 from dual;
 
           1
------------
           1
 
1 row fetched.
 
SQL> select 2 from dual;
 
           2
------------
           2
 
1 row fetched.
```

<span id="sqlfile" name="sqlfile"></span>
**Method 2**: By using `@` or `@@` method

You can execute local SQL files using the `@filename[.ext]` or `@@filename[.ext]` command, and the extension can be omitted. The specific usage is as follows:

- After logging into the database, directly execute `@filename[.ext]` or `@@filename[.ext]` in the *yasql* command-line interface.
- Directly execute `yasql username/password@host:port @filename[.ext]` or `yasql username/password@host:port @@filename[.ext]` to log in to the database and execute local SQL files; the login status and *yasql* command-line interface will be retained after execution.

When entering `@file_name.ext` or `@@file_name.ext` interactively, `@` or `@@` will look for the required file in the current working directory. When the SQL file is in the current working directory of the *yasql* tool, you do not need to specify the file's path; otherwise, the path of the file must be specified (absolute or relative path is acceptable).

In the absence of nested script calls, `@` and `@@` commands are equivalent.

If the script `s_1.sql` contains a command `@@ s_2.sql`, when calling the `s_1.sql` script that nests `s_2.sql`, `@@` will look for the nested script `s_2.sql` in the same path as `s_1.sql` (rather than the current working directory).

***Example***

```sql
-- s.sql is in the same directory as *yasql*
@s.sql
 
           1
------------
           1
 
1 row fetched.

           2
------------
           2
 
1 row fetched.

-- Absolute path
@/home/yasdb/s.sql
 
           1
------------
           1
 
1 row fetched.

           2
------------
           2
 
1 row fetched.
 
-- Relative path
@../s.sql
 
           1
------------
           1
 
1 row fetched.

           2
------------
           2
 
1 row fetched.
```

### Execute PL Statements

After logging into the database, you can directly execute PL statements in the *yasql* command-line interface. When executing PL statements, you need to add "/" after the procedure body.

***Example***

```sql
set serveroutput on
  
DECLARE
i int;
BEGIN
i := 0;
IF i < 5 THEN
i := i + 1;
DBMS_OUTPUT.PUT_LINE ('This is: '||i);
END IF;
END;
  /
 
This is: 1
PL/SQL Succeed.
```

<span id="shell" name="shell"></span>

### Execute Shell Commands

In the *yasql* command-line interface, you can execute OS-level shell commands with `!`.

***Example***

```sql
-- Execute external OS command date in *yasql*
!date
Tue Aug  3 14:11:39 CST 2021
```

<span id="lastlogin" name="lastlogin"></span>

### Display Last Login Info

When [enabling audit function](../../Product Security/Security Audit/Audit Basic Configuration) and [creating and enabling](../../Product Security/Security Audit/Audit Policy Management) an audit policy that contains the LOGON system behavior, you can display the user's last login record by enabling the LASTLOGININFO attribute of the client when logging into YashanDB with *yasql*. The record information includes:

- If the current login is successful, the last login record will be displayed:

  - Last successful login: displays date, time, client hostname or IP, etc.
  
  - Last failed login: displays date, time, accessing application, IP address and access method (client type, connection method), and the number of failed attempts.

  - No last login exists (i.e., this is the first login): no information.

- If the current login fails, no information is displayed.

To enable the LASTLOGININFO attribute of the client, use the following methods:

- Permanent effect: Configure through the yasql.ini configuration file.

  1. Check if the client/yasql.ini file exists under the YashanDB client folder; if not, create it.

  2. Add the following configuration to the yasql.ini file and save it.

    ```ini
    LASTLOGININFO = on
    ```

- Current window effect: After opening the *yasql* command window, set it using the `set lastlogin on|off` command; default is off.

***Example***

```sql
-- Enable audit function and prepare audit policy

ALTER SYSTEM SET UNIFIED_AUDITING=TRUE;
CREATE AUDIT POLICY logon ACTIONS LOGON;
AUDIT POLICY logon;

set lastlogin on
conn sales/********

Last Successful login time: 2025-02-28 15:25:31.235728 Program: yasql Client message: (TYPE=(DATABASE));(CLIENT ADDRESS=((PROTOCOL=uds)(HOST=localhost)(PORT=0)))

conn sales1/********

Last Failed login time: 2025-02-28 15:17:57.240383 and the last 3 consecutive login attempts failed, Program: yasql Client message: (TYPE=(DATABASE));(CLIENT ADDRESS=((PROTOCOL=uds)(HOST=localhost)(PORT=0)))
```

### Terminate SQL Operations

To terminate the current SQL process, there are the following methods:

- Press **Ctrl**+**C** in the *yasql* command-line interface. If there is an SQL statement currently being executed, *yasql* will send a cancel message to the server, which will terminate the current SQL.
- The process logged in with *yasql* will exit when terminated at the operating system level.

<span id="show" name="show"></span>

### View Parameter Information

- Use `show parameter param_name` to view the [configuration parameter](../../Reference Manual/Configuration Parameters) information of YashanDB.
- Use `show param_name` to view the parameter information of *yasql* (the parameters configured via set commands), such as [NUM\[WIDTH\]](#numwidth), [AUTO\[COMMIT\]](#autocommit), etc.

***Example***

```sql 
SHOW PARAMETER DB_BLOCK_SIZE;
NAME                   VALUE    
---------------------- ----------
DB_BLOCK_SIZE          8192

SHOW SERVEROUT
serveroutput OFF
```
<span id="show_pdbs" name="show_pdbs"></span>

### View PDBs Information


In a CDB, PDB information can be viewed through `show pdbs`, including container ID, container name, and running status.  

- Connect to the CDB root and execute this command to query information of all PDBs in the current environment (including the built-in PDB seed).  

- Directly connect to a specific PDB and execute this command to query information of the current PDB.  

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
# Connect to the CDB root and execute this command
$ yasql c##sales/********@192.168.1.2:1688
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

SQL> show pdbs

               CON_ID CON_NAME                                                         STATUS
--------------------- ---------------------------------------------------------------- -----------------
                    1 PDB$SEED                                                         CLOSED
                    2 PDB1                                                             OPEN

SQL> exit

# Directly connect to pdb1 and execute this command
$ yasql sales/********@192.168.1.2:1688/pdb1
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

SQL> show pdbs

               CON_ID CON_NAME                                                         STATUS
--------------------- ---------------------------------------------------------------- -----------------
                    2 PDB1                                                             OPEN
```


<span id="desc" name="desc"></span>

### Get Description Information

You can retrieve description information of a database object using the `describe` or `desc \<table name>|\<view name>` command.

***Example***

```sql
desc v$database
NAME                                                             NULL?     DATATYPE                          
---------------------------------------------------------------- --------- --------------------------------- 
DATABASE_ID                                                                BIGINT                            
DATABASE_NAME                                                              VARCHAR(64)                       
CREATE_TIME                                                                DATE                              
LOG_MODE                                                                   VARCHAR(16)                       
OPEN_MODE                                                                  VARCHAR(16)                       
PROTECTION_MODE                                                            VARCHAR(32)                       
PROTECTION_LEVEL                                                           VARCHAR(32)                       
DATABASE_ROLE                                                              VARCHAR(16)                       
BLOCK_SIZE                                                                 INTEGER                           
CURRENT_SCN                                                                BIGINT                            
STATUS                                                                     VARCHAR(32)                       
RCY_POINT                                                                  VARCHAR(32)                       
FLUSH_POINT                                                                VARCHAR(32)                       
RESET_POINT                                                                VARCHAR(32)                       
PLATFORM_NAME                                                              VARCHAR(256)                      
HOST_NAME                                                                  VARCHAR(256)                      
RESTORE_TIME                                                               DATE                              
SWITCHOVER_STATUS                                                          VARCHAR(32)                       
SUPPLEMENTAL_LOG_DATA_MIN                                                  VARCHAR(8)                        
SUPPLEMENTAL_LOG_DATA_PK                                                   VARCHAR(8)                        
SUPPLEMENTAL_LOG_DATA_ALL                                                  VARCHAR(8)                        
SUPPLEMENTAL_LOG_TABLE_TYPE                                                VARCHAR(16)                        
```

<span id="directexecute" name="directexecute"></span>

### Set Direct Execution

You can set whether to enable direct execution of SQL statements with `set DIRE\[CTEXECUTE\] on|off`, with the default being on.

*   on: A SQL statement executes with one message interaction.

*   off: A SQL statement executes with two message interactions.

***Example***

```sql
set directexecute on
set directexecute off

show directexecute
directexecute OFF
```

<span id="autocommit" name="autocommit"></span>

### Set Automatic Transaction Commit

You can set whether to enable automatic commit for transactions with the command `set auto\[commit\] on|off`, with the default being off. When autocommit is on, every executed SQL statement will automatically commit the transaction, and you can use `show autocommit` to check the current configuration.

***Example***

```sql
set autocommit on
set autocommit off
set auto on
set auto off

show autocommit
autocommit OFF
```

<span id="timing" name="timing"></span>

### Set Execution Time Statistics

You can set whether to enable execution time statistics with `set timi\[ng\] on|off`, with the default being off. The time display format is "Elapsed: hh:mm:ss.ff".

***Example***

```sql
set timing on
SELECT open_mode FROM v$database;
 
OPEN_MODE        
-----------------
READ_WRITE      
 
1 row fetched.
 
Elapsed: 00:00:00.000

set timing off
SELECT open_mode FROM v$database;
 
OPEN_MODE        
-----------------
READ_WRITE      
 
1 row fetched.
```

<span id="feedback" name="feedback"></span>

### Set Display Feedback Information

You can set whether to display feedback information with `set FEED\[BACK\] on|off`, with the default being on.

- on: Display feedback information.
- off: Do not display feedback information.

Feedback information may be "Succeed.", "PL/SQL Succeed.", or "ROW_NUM row/rows affected." but does not include error codes.

***Example***

```sql
set feedback on
select * from dual;
 
DUMMY
-----
X
 
1 row fetched.
 
set feedback off
select * from dual;
DUMMY
-----
X

show feedback
feedback OFF
```

<span id="serveroutput" name="serveroutput"></span>

### Set Print Cache Information

You can set whether to print information from the server-side buffer configured by [DBMS_OUTPUT](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_OUTPUT).PUT_LINE with `set SERVEROUT[PUT] on|off`, with the default being OFF.

- on: For each executed SQL statement or anonymous PL call, *yasql* will call DBMS_OUTPUT.GET_LINES() to obtain and print the remaining information from the cache.
- off: Close the printing switch.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
set serveroutput on;
BEGIN
    DBMS_OUTPUT.ENABLE(3000);
    DBMS_OUTPUT.PUT('yashanDB');
    DBMS_OUTPUT.PUT_LINE('hello world!');
    DBMS_OUTPUT.PUT('coming'); 
END;
/

--result
yashanDBhello world!

PL/SQL Succeed.

set serveroutput off
SQL> BEGIN
   2     DBMS_OUTPUT.ENABLE(3000);
   3     DBMS_OUTPUT.PUT('yashanDB');
   4     DBMS_OUTPUT.PUT_LINE('hello world!');
   5     DBMS_OUTPUT.PUT('coming');
   6 END;
   7 /

PL/SQL Succeed.
```

<span id="spool" name="spool"></span>

### Output Saved as File

The `SPOOL` command is used to save the output of the *yasql* session to a file, command format:

```sql
SPO[OL] [file_name[.ext] [CRE[ATE] | REP[LACE] | APP[END]] | OFF]
```

**file_name[.ext]**

Specifies the file name used to save the output results, the naming rules are as follows:

- If `file_name` contains spaces, it must be enclosed in quotes.
- The file extension can be omitted; if omitted, the default extension will be `.LST` (in Windows, in all uppercase) or `.lst` (in Linux, in all lowercase).

The file extension will not be appended to system files such as `/dev/null` and `/dev/stderr`.

**CRE[ATE]**

Creates a new file with the specified name; in this case, `file_name[.ext]` cannot be the same as an existing file.

**REP[LACE]**

Replaces an existing file with the output results; in this case, `file_name[.ext]` must specify an existing file name.

When the option `[CRE[ATE] | REP[LACE] | APP[END]` is not specified, the default is `REP[LACE]`, that is, `SPO[OL] [file_name[.ext]` is equivalent to `SPO[OL] [file_name[.ext] REP[LACE]`.

**APP[END]**

Appends the output results to an existing file; in this case, `file_name[.ext]` must specify an existing file name.

**OFF**

Stops saving the output content to a file.

The typical process of using the `SPOOL` command in a *yasql* session is as follows:

1. Execute `SPO[OL] file_name[.ext] [CRE[ATE] | REP[LACE] | APP[END]` to start saving to a file.
2. Execute other SQL statements (e.g., SELECT statements, etc.); all output results will be saved to the specified file.
3. Execute `SPO[OL] OFF` to stop saving.

If `SPO[OL] file_name[.ext] [CRE[ATE] | REP[LACE] | APP[END]` is executed again after the first execution, it is equivalent to executing `SPO[OL] OFF` between the two SPOOL commands.

***Example***

```sql
spool employees.txt create
select * from employees;
spool off
```

After executing the above example, a file named `employees.txt` will be created, and the query results will be saved to that file.

<span id="autotrace" name="autotrace"></span>

###  Generate AUTOTRACE Report

Use the `set AUTOTRACE` command to enable or disable the generation of execution plan analysis reports for DML statements (SELECT/INSERT/UPDATE/DELETE), and to specify which information sections should be returned. For detailed information about the report, please refer to [AUTOTRACE](../../Performance Tuning/Performance Tuning Features and Tools/AUTOTRACE).

Command format:

```sql
SET AUTOT[RACE] ON|TRACE[ONLY]|OFF [EXP[LAIN]] [STAT[ISTICS]]
```

- ON: Outputs the execution results and execution plan analysis report for the SQL statement, including both the execution plan and execution statistics — equivalent to ON EXP[LAIN] STAT[ISTICS].

  - ON EXP[LAIN]: Outputs the execution results and execution plan.

  - ON STAT[ISTICS]: Outputs the execution results and execution statistics.

- TRACE[ONLY] [EXP[LAIN]] [STAT[ISTICS]]: Generates only the execution plan analysis report (with the same information sections as above), without outputting the execution results of the SQL statement.

- OFF (default): Does not generate any report.

> **Note**:
>
> Statistics are recorded only when the STATISTICS_LEVEL parameter is set to TYPICAL or ALL.

<span id="nchar" name="nchar"></span>

### Set NCHAR Literal Replacement

You can set whether to enable NCHAR literal replacement with `set nchar\[_literal_replace\] true|false`, with the default being false.

***Example***

```sql
SELECT N'12%',
TYPEOF(N'12%') TYPE
FROM DUAL;
N'12%'            TYPE
----------------- ---------
12%               nchar

set nchar_literal_replace true
SELECT N'12%',
TYPEOF(N'12%') TYPE
FROM DUAL;
U'\0031\0032\0025' TYPE
------------------ ---------
12%                nchar
```

<span id="set_def" name="set_def"></span>

### Set Placeholder Replacement

You can set whether to enable scanning and replacing command placeholders "&" variables with `set DEF[INE] ON or OFF`, with the default being on. You can use `show DEF[INE]` to check the current configuration.

- on: Replace placeholders.
- off: Do not replace placeholders.

***Example***

```sql
set define on
set define off

show define
define OFF
```

When the DEFINE switch is ON, *yasql* will search and replace variable placeholders "&" in commands.

If a variable is assigned a value using DEFINE in advance, there will be no prompt; otherwise, *yasql* will prompt for input of the corresponding value to assign to the variable.

***Example***

```sql
SELECT '&yasql' FROM dual;
Enter value for yasql: YashanDB
old     1: SELECT '&yasql' FROM dual
new     1: SELECT 'YashanDB' FROM dual

'YASHANDB'
----------
YashanDB

1 row fetched.
```

You can also assign values to variables using `ACC[EPT] variable [CHAR] [PROMPT text|NOPR[OMPT]] [HIDE]` syntax.

***Example***

```sql
ACCEPT username CHAR PROMPT 'Please input username: ';
yashan
ACCEPT password CHAR PROMPT 'Please input password: '  HIDE;
yashan
conn &username/&password
```

<span id="DEFINE" name="DEFINE"></span>

### Variable Assignment

You can assign values to variables (CHAR values) using the DEFINE directive and then use that variable in SQL statements.

Inputting DEFINE without clauses will list all replacement variable values and types.

If a variable is assigned the number 20, *yasql* will specify a CHAR value, which consists of the characters 2 and 0. 

If a variable is assigned values in advance using DEFINE, there will be no prompt; otherwise, *yasql* will prompt for input of the corresponding value.

***Example***

```sql
-- Define variables
DEFINE POS = MANAGER
DEFINE DEPARTMENT_ID = 20
-- Query variables
DEFINE
DEFINE DEPARTMENT_ID           = "20" (CHAR)
DEFINE POS             = "MANAGER" (CHAR)
-- Use variables
SELECT '&POS' FROM dual;
old     1: SELECT '&POS' FROM dual
new     1: SELECT 'MANAGER' FROM dual

'MANAGER'
---------
MANAGER

1 row fetched.

SELECT '&DEPARTMENT_ID' FROM dual;
old     1: SELECT '&DEPARTMENT_ID' FROM dual
new     1: SELECT '20' FROM dual

'20'
-----
20

1 row fetched.
```

<span id="numwidth" name="numwidth"></span>

### Set Display Width

When outputting floating-point and NUMBER type data, you can set the display width using the command `set num\[width\] <1~128>`. The default width is 10, with a range of [1,128].

***Example***

```sql
show num
numwidth 10

CREATE TABLE item(ino INT, price DOUBLE);
 
Succeed.
 
INSERT INTO item VALUES(1, 57.6783924);
 
1 row affected.
 
SELECT INO,PRICE FROM item;
 
         INO       PRICE
------------ -----------
           1  5.768E+001
 
1 row fetched.
 
set num 20
SELECT INO,PRICE FROM item;
 
         INO                 PRICE
------------ ---------------------
           1       5.76783924E+001
 
1 row fetched.
```

<span id="heading" name="heading"></span>

### Set Print Column Headers

You can set whether to print column headers in query results with `set HEA\[DING\] on|off`, with the default being on.

- on: Print column headers.
- off: Do not print column headers. The `SET HEADING OFF` command will not affect the displayed column widths.

***Example***

```sql
set heading on
select * from dual;
 
DUMMY
-----
X
 
1 row fetched.
 
set heading off
select * from dual;
 
X
 
1 row fetched.
 
show heading
heading OFF
```


<span id="COLUMN" name="COLUMN"></span>

### Manage Column Display Attributes

#### Specify Column Display Attributes

Specify display attributes for the target column. Identify the target by column name; if querying columns with the same name from different tables, the COLUMN command for that column name will apply to all columns with the same name.

Command format:

```sql
-- Format 1
COL/COLUMN <column name> FOR/FORMAT <format string>

-- Format 2
COL/COLUMN "<column name>" FOR/FORMAT "<format string>"

-- Format 3
COL/COLUMN '<column name>' FOR/FORMAT '<format string>'
```

- The COLUMN command is case insensitive and does not use wildcards.
- Generally, using single/double quotes or no quotes works, but if special symbols are involved, the quotes must be used.
- If you need to use an expression in the COLUMN command, it must be exactly the same as the expression in the SELECT command. For example, if the expression in the SELECT command is a+b, the COLUMN command must also be a+b; it cannot be changed to b+a or (a+b) or anything else.
- The length of "<column name>" is in the range of [1,64] bytes.

The usage rules for specifying column display attributes are as follows:

- Currently, only character columns can use the COLUMN command, and their default width is the width of the column in the database. If you need to adjust the display width, you should use FORMAT An, where n is an integer in the range of [1,32000]. If the specified width is less than the string length, the elements in the column will be displayed directly as truncated (truncate).
- If you use the COLUMN command on a numeric column, that column will display as a string of "#".

***Example***

```sql
select deparment_no,department_name from department;

DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep

4 rows fetched.

col department_name format a10;
select deparment_no,department_name from department;

DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep

4 rows fetched.

col department_name format a6;
select deparment_no,department_name from department;

DEPARMENT_NO DEPA
------------ ----
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep

4 rows fetched.
```

#### View Column Display Attributes

- Use the `COL/COLUMN <column name>` command to view the current display attributes of a specified single column.
- Use the `COL/COLUMN` command to view the current display attributes of all columns.

***Example***

```sql
-- View display attribute values of a single column
col col1
COLUMN	 col1 ON
FORMAT	 a11

-- View display attribute values of all columns
col
COLUMN	 col1 ON
FORMAT	 a11
```

#### Reset/Clear Column Display Attributes

- You can reset the display attributes of a single column using the CLEAR clause of the COLUMN command.
- Use `CL/CLEAR COL/COLUMN/COLUMNS` to reset the column display attributes set by the COLUMN command to their default settings.
***Example***

```sql
-- Clear the display attribute value of a single column
col col2 clear
col
COLUMN	 col1 ON
FORMAT	 a11

-- Clear the display attribute value of all columns
CLEAR COLUMNS
columns cleared
```

<span id="EXIT" name="EXIT"></span>

### Exit Login

#### Manual Exit

You can exit *yasql* using the command `EXIT [SUCCESS | FAILURE | WARNING | n ] [COMMIT | ROLLBACK]`.

`EXIT` can specify the return code for the operating system. The keywords SUCCESS, WARNING, and FAILURE represent values related to the operating system. In Linux, the return code has only one byte of storage; thus, the range for operating system return codes is limited to 0-255.

By default, executing `EXIT` will perform `EXIT SUCCESS COMMIT`. If there is a syntax error in the EXIT options or a non-numeric variable is used, *yasql* will perform `EXIT FAILURE COMMIT`.

```sql
-- Exit directly
EXIT

-- Exit after performing a rollback operation and return 99
EXIT 99 rollback

-- Exit after committing all uncommitted transactions, returning the error code of the last executed SQL command or PL block
EXIT SQL.SQLCODE

-- Exit with a success state, returning 0 indicating success
EXIT SUCCESS

-- Exit with a failure state, returning 1 indicating failure
EXIT FAILURE

-- Exit with a warning state, returning 2 indicating a warning
EXIT WARNING
```

#### Automatic Exit on Error

You can activate automatic exit on error with the command `WHENEVER SQLERROR EXIT [SUCCESS | FAILURE | WARNING | n]  [COMMIT | ROLLBACK]`, meaning that when a SQL command or PL block generates an error, the corresponding EXIT operation will be executed. The syntax for EXIT here is completely consistent with the EXIT statements utilized in manual exit.

> **Note**:
> 
> This only takes effect in error scenarios where the error code is YAS and only functions within the current *yasql* process. It does not take effect in error scenarios where the error code is YASQL. When `set serveroutput on` is activated, the error occurred in the anonymous block does not take effect.

```sql
-- Exit directly upon error
WHENEVER SQLERROR EXIT
insert test;

[1:8]YAS-04115 "INTO" expected but missing

-- Exit after performing rollback upon error, returning 99
WHENEVER SQLERROR EXIT 99 rollback
insert test;

[1:8]YAS-04115 "INTO" expected but missing

-- Exit after committing all uncommitted transactions upon error and return the last executed SQL command or PL block's error code
WHENEVER SQLERROR EXIT SQL.SQLCODE
insert test;

[1:8]YAS-04115 "INTO" expected but missing
```

### Disconnect

Use the `disconnect` command or `disc` command to disconnect from the database. After disconnecting, SQL statements cannot be executed, and you can re-login with the `conn` command.

```sql
disc
```
