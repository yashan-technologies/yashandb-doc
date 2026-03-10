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
$ yasql [-S] username/password@host:port [-c "SQL" | -f [-e]  filename[.ext] | @filename[.ext]] 
$ yasql [-S] / as sysdba [-c "SQL" | -f [-e]  filename[.ext] | @filename[.ext]]
$ yasql <command>
```

**-S**

Used to specify [silent login](#login). Ensure that `-S` is the first parameter, and this parameter is case insensitive.

**username/password**
**/**

Specify the database username and password for [logging in or connecting](#login) to the database.

If the username or password contains special symbols (except for underscores), such as `@`, `/`, `.`, `!`, `$`, `'`, etc., the corresponding values must be enclosed in double quotes during [creation](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/CREATE USER)/[management](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/ALTER USER). To ensure that *yasql* can correctly pass the username and password values to the database server, it is recommended to use [step-by-step login](#nolog) when there are special characters; in this case, only double quotes are needed. If logging in directly, the characters must be rewritten accordingly:

- If the username or password does not already contain single quotes, it can be directly enclosed in single quotes for transmission, such as `user@name`, `password$` should be escaped and rewritten as `'"user@name"'/'"password$"'`.
- If the username or password already contains single quotes, it cannot be transmitted using single quotes, and escaping and double quotes should be used accordingly:

  - If there are both single quotes and functionality symbols in environments like Linux, such as variable substitution symbols, escape symbols, etc., like `\`, `$`, `"`, `'`, multiple levels of escaping are required, such as `user'1$name`, `pass'1\word` should be rewritten as `"\"user'1\$name\""/"\"pass'1\\word\""`.

  - If there is a single quote but no aforementioned functionality symbols, double quotes must be used, such as `user'name`, `password` should be rewritten as `"\"user'name\""/password`.

> **Note**: 
>
> To correctly pass the username to the database server, users created using *yasql* in mysql mode connecting to YashanDB (mysql mode) need to be additionally enclosed in double quotes.

When logging in via OS authentication, the username and password can be omitted, but `/` cannot be omitted. OS authentication login requires configuration before use; please refer to the database administration manual for [OS authentication configuration](../../Database Administration/Basic Database Management/OS Authentication Configuration).

**@host:port**

Specify the URL of the target database to log in or connect to; for details, please refer to [login and connection](#login).

**as sysdba**

Identifier for logging in via OS authentication.

**-c "SQL"**

Used to log in and execute a single SQL statement (and exit immediately after execution); for details, please refer to [executing SQL statements](#singlesql).

**-f [-e] filename[.ext]**

Used to execute SQL files; for details, please refer to [executing SQL files](#fsql).

**@filename[.ext]**

Used to execute SQL files; for details, please refer to [executing SQL statements](#sqlfile).

**command**

Specific command statements, including [/nolog](#nolog), set commands, etc.

## Functionality Introduction

<span id="login" name="login" class="yaslink"></span>

### Login and Connection

Login methods can be divided into the following types:

- Method 1: Direct login, command format: `yasql username/password@host:port`.
- Method 2: Silent login, command format: `yasql -S username/password@host:port`. Using this method to log in and connect to the database will not return command prompts, command echo, login prompt information, and version information.
- Method 3: OS authentication login, command format: `yasql / as sysdba` or `yasql username/password as sysdba`. Using this method does not require a configured connection, and can only log in to the database instance corresponding to the local YASDB_DATA environment variable.
<span id="nolog" name="nolog" class="yaslink"></span>
* Method 4: Step-by-step login:
    1. Start *yasql* using the `/nolog` command, command format: `yasql /nolog`. At this point, only the login window opens, but the database is not connected.

    2. Use the `connect` or `conn` command to connect and log in to the database, command format: `connect username/password@host:port` or `conn username/password@host:port`.

- Method 5: Switching logins, that is, using the `connect` or `conn` command to switch to another user after logging in.

Connection methods can be divided into the following types:

- Method 1: IPv4 connection with URL, for example, `@192.168.1.2:1688`.
- Method 2: IPv6 connection with URL, the IP must be enclosed in square brackets, and link-local IPv6 addresses must use the percentage symbol to identify the network interface name. The system network card must support IPv6 connections, currently supporting:

  - IPv6 loopback address, for example, `@[::1]:1688`

  - Link-local address, for example, `@[fe80::a89b:8fdb:2c28:35ce%ens192]:1688`

  - Unique local address, for example, `@[fc00:7::126]:1688`

  - Unspecified address, for example, `@[::]:1688`

  - Global unicast address, for example, `@[2001:250:4000:4000:f80f:6d4c:4310:35ce]:1688`

  - IPv4-mapped IPv6 address, for example, `@[::ffff:192.168.7.126]:1688`

- Method 3: Domain name connection with URL, for example, `@yashandb123.vip:1688`. The domain name must support resolution to an existing database server IP address; if the current domain resolves to multiple IP addresses, it will attempt to connect to these IP addresses in order until successful.
- Method 4: Connection without URL, i.e., using [UDS local connection](../../产品安全/连接管理/00连接管理), indicating connection to the local database instance corresponding to the $YASDB_DATA environment variable. Using this connection method is a high privilege operation and should be used judiciously.
- Method 5: Alias connection, for example, `@alias`, which must be previously defined in `${YASDB_HOME}/client/yasc_service.ini`.

***Example***

```shell
# Direct login + IPv4 connection with URL
$ yasql sales/********@192.168.1.2:1688
YashanDB SQL Enterprise Edition Release {version number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version number} x86_64 - Linux
SQL>

# Direct login + IPv6 loopback address connection
$ yasql sales/********@[::1]:1688
YashanDB SQL Enterprise Edition Release {version number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version number} x86_64 - Linux
SQL>

# Silent login + Domain name connection with URL
$ yasql -S sales/sales@123.com:1688

# OS authentication login
$ yasql / as sysdba
YashanDB SQL Enterprise Edition Release {version number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version number} x86_64 - Linux
SQL>

# Direct login + Alias connection
$ cat ${YASDB_HOME}/client/yasc_service.ini
local = 127.0.0.1:1688
$ yasql sales/********@local
Connected to:
YashanDB Server Enterprise Edition Release {version number} x86_64 - Linux
SQL>

# Step-by-step login
$ yasql /nolog
YashanDB SQL Enterprise Edition Release {version number} x86_64
SQL> conn sales/sales@192.168.1.2:1688
YashanDB SQL Enterprise Edition Release {version number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version number} x86_64 - Linux
-- Login successful

-- Switch login: switch to another user via username and password + connection without URL
SQL> conn sales1/sales1

-- Switch login: switch to another user via username and password + IPv4 connection with URL
SQL> conn sales1/sales1@192.168.1.3:1688
```

### Execute SQL Statement

**Method 1**: Execute SQL statements after logging in

After logging in to the database, you can directly execute SQL statements in the *yasql* command-line interface.

***Example***

```shell
$ yasql sales/********@192.168.1.2:1688
YashanDB SQL Enterprise Edition Release {version number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version number} x86_64 - Linux
SQL> CREATE TABLE customer(cno INT, cname VARCHAR(50));
 
 
SQL> INSERT INTO customer VALUES(1, 'customer1');
 
 
SQL> SELECT cno,cname FROM customer;
 
         CNO CNAME                                                
------------ -----------------------------------------------------
           1 customer1                                           
 
```

<span id="singlesql" name="singlesql" class="yaslink"></span>
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

<span id="fsql" name="fsql" class="yaslink"></span>
**Method 1**: By using the `-f\[-e\]` parameter

Using the `-f \[-e\] filename[.ext]` parameter, you can directly connect and log in to the database to execute a SQL file, and the file extension can be omitted.

- `-f` specifies the SQL file to execute.
- `-e` is used to display the specific statements being executed in the file.

When the SQL file is in the same directory as the *yasql* tool, you do not need to specify the file path; otherwise, the path of the file must be specified (absolute or relative path is acceptable).

***Example***

```sql
-- s.sql is in the same directory as *yasql*
$ yasql sales/******** -f s.sql
SQL> SELECT 1 FROM dual;
 
           1
------------
           1
 
 
SELECT 2 FROM dual;
 
           2
------------
           2
 
```

<span id="sqlfile" name="sqlfile" class="yaslink"></span>
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
 

           2
------------
           2
 

-- Absolute path
@/home/yasdb/s.sql
 
           1
------------
           1
 

           2
------------
           2
 
 
-- Relative path
@../s.sql
 
           1
------------
           1
 

           2
------------
           2
 
```

<span id="spool" name="spool" class="yaslink"></span>

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
SELECT * FROM employees;
spool off
```

After executing the above example, a file named `employees.txt` will be created, and the query results will be saved to that file.

### Execute PL Statements

After logging into the database, you can directly execute PL statements in the *yasql* command-line interface. When executing PL statements, you need to add "/" after the procedure body.

***Example***

```sql
SET serveroutput ON
  
DECLARE
i INT;
BEGIN
i := 0;
IF i < 5 THEN
i := i + 1;
DBMS_OUTPUT.PUT_LINE ('This is: '||i);
END IF;
END;
  /
 
This is: 1
```

### Execute Shell Commands

In the *yasql* command-line interface, you can execute OS-level shell commands with `!`.

***Example***

```sql
-- Execute external OS command date in *yasql*
!date
Tue Aug  3 14:11:39 CST 2021
```

### Terminate SQL Operations

To terminate the current SQL process, there are the following methods:

- Press **Ctrl**+**C** in the *yasql* command-line interface. If there is an SQL statement currently being executed, *yasql* will send a cancel message to the server, which will terminate the current SQL.
- The process logged in with *yasql* will exit when terminated at the OS level.

### View Parameter Information

- Use `show parameter param_name` to view the configuration parameter information of YashanDB.
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

<span id="numwidth" name="numwidth" class="yaslink"></span>

### Set Display Width

When outputting floating-point and NUMBER type data, you can set the display width using the command `set num\[width\] <1~128>`. The default width is 10, with a range of [1,128].

***Example***

```sql
show num
numwidth 10

CREATE TABLE item(ino INT, price DOUBLE);
 
 
INSERT INTO item VALUES(1, 57.6783924);
 
 
SELECT INO,PRICE FROM item;
 
         INO       PRICE
------------ -----------
           1  5.768E+001
 
 
SET num 20
SELECT INO,PRICE FROM item;
 
         INO                 PRICE
------------ ---------------------
           1       5.76783924E+001
 
```
<span id="autocommit" name="autocommit" class="yaslink"></span>

### Set Auto Commit for Transactions

You can set whether to enable auto commit with the command `set auto\[commit\] on|off`, with the default being off. When autocommit is on, every executed SQL statement will automatically commit the transaction, and you can use `show autocommit` to check the current configuration.

***Example***

```sql
SET autocommit ON
SET autocommit OFF
SET auto ON
SET auto OFF

show autocommit
autocommit OFF
```

<span id="timing" name="timing" class="yaslink"></span>

### Set Execution Time Statistics

You can set whether to enable execution time statistics with `set timi\[ng\] on|off`, with the default being off. The time display format is "Elapsed: hh:mm:ss.ff".

***Example***

```sql
SET timing ON
SELECT open_mode FROM V$DATABASE;
 
OPEN_MODE        
-----------------
READ_WRITE      
 
 
Elapsed: 00:00:00.000

SET timing OFF
SELECT open_mode FROM V$DATABASE;
 
OPEN_MODE        
-----------------
READ_WRITE      
 
```

### Set Direct Execution

You can set whether to enable direct execution of SQL statements with `set DIRE\[CTEXECUTE\] on|off`, with the default being on.

*   on: A SQL statement executes with one message interaction.

*   off: A SQL statement executes with two message interactions.

***Example***

```sql
SET directexecute ON
SET directexecute OFF

show directexecute
directexecute OFF
```

### Set Print Cache Information

You can set whether to print the cache information set by the server [DBMS_OUTPUT](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_OUTPUT).PUT_LINE with `set SERVEROUT[PUT] on|off`, with the default being OFF.

- on: For each executed SQL statement or anonymous PL call, *yasql* will call DBMS_OUTPUT.GET_LINES() to obtain and print the remaining information from the cache.
- off: Close the printing switch.

***Example*** for Standalone Deployment and YAC Deployment

```plsql
SET serveroutput ON;
BEGIN
    DBMS_OUTPUT.ENABLE(3000);
    DBMS_OUTPUT.PUT('yashanDB');
    DBMS_OUTPUT.PUT_LINE('hello world!');
    DBMS_OUTPUT.PUT('coming'); 
END;
/

--result
yashanDBhello world!


SET serveroutput OFF
BEGIN
   2     DBMS_OUTPUT.ENABLE(3000);
   3     DBMS_OUTPUT.PUT('yashanDB');
   4     DBMS_OUTPUT.PUT_LINE('hello world!');
   5     DBMS_OUTPUT.PUT('coming');
   6 END;
   7 /

```

### Set Display Feedback Information

You can set whether to display feedback information with `set FEED\[BACK\] on|off`, with the default being on.

- on: Display feedback information.
- off: Do not display feedback information.

Feedback information may be "Succeed.", "PL/SQL Succeed.", or "ROW_NUM row/rows affected." but does not include error codes.

***Example***

```sql
SET feedback ON
SELECT * FROM dual;
 
DUMMY
-----
X
 
 
SET feedback OFF
SELECT * FROM dual;
DUMMY
-----
X

show feedback
feedback OFF
```

### Set Print Column Headers

You can set whether to print the column headers in the displayed report with `set HEA\[DING\] on|off`, with the default being on.

- on: Print column headers.
- off: Do not print column headers. The `SET HEADING OFF` command will not affect the displayed column widths.

***Example***

```sql
SET heading ON
SELECT * FROM dual;
 
DUMMY
-----
X
 
 
SET heading OFF
SELECT * FROM dual;
 
X
 
 
show heading
heading OFF
```

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

SET nchar_literal_replace true
SELECT N'12%',
TYPEOF(N'12%') TYPE
FROM DUAL;
U'\0031\0032\0025' TYPE
------------------ ---------
12%                nchar
```

### Set Placeholder Replacement

You can set whether to enable scanning and replacing command placeholders "&" variables with `set DEF[INE] ON or OFF`, with the default being on. You can use `show DEF[INE]` to check the current configuration.

- on: Replace placeholders.
- off: Do not replace placeholders.

***Example***

```sql
SET define ON
SET define OFF

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


SELECT '&DEPARTMENT_ID' FROM dual;
old     1: SELECT '&DEPARTMENT_ID' FROM dual
new     1: SELECT '20' FROM dual

'20'
-----
20

```

### Column Display Attributes

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
- The length of "\<column name>" is in the range of [1,64] bytes.

The usage rules for specifying column display attributes are as follows:

- Currently, only character columns can use the COLUMN command, and their default width is the width of the column in the database. If you need to adjust the display width, you should use FORMAT An, where n is an integer in the range of [1,32000]. If the specified width is less than the string length, the elements in the column will be displayed directly as truncated (truncate).
- If you use the COLUMN command on a numeric column, that column will display as a string of "#".

***Example***

```sql
SELECT deparment_no,department_name FROM department;

DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep


col department_name format a10;
SELECT deparment_no,department_name FROM department;

DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep


col department_name format a6;
SELECT deparment_no,department_name FROM department;

DEPARMENT_NO DEPA
------------ ----
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep

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

### Exit Login

#### Manual Exit

You can exit *yasql* using the command `EXIT [SUCCESS | FAILURE | WARNING | n ] [COMMIT | ROLLBACK]`.

`EXIT` can specify the return code for the OS. The keywords SUCCESS, WARNING, and FAILURE represent values related to the OS. In Linux, the return code has only one byte of storage; thus, the range for OS return codes is limited to 0-255.

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
INSERT test;

[1:8]YAS-04115 "INTO" expected but missing

-- Exit after performing rollback upon error, returning 99
WHENEVER SQLERROR EXIT 99 rollback
INSERT test;

[1:8]YAS-04115 "INTO" expected but missing

-- Exit after committing all uncommitted transactions upon error and return the last executed SQL command or PL block's error code
WHENEVER SQLERROR EXIT SQL.SQLCODE
INSERT test;

[1:8]YAS-04115 "INTO" expected but missing
```

### Disconnect

Use the `disconnect` command or `disc` command to disconnect from the database. After disconnecting, SQL statements cannot be executed, and you can re-login with the `conn` command.

```sql
disc
```
