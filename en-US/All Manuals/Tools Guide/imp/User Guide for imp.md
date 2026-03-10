View Help and Version Information
----

Use the command `imp -h` or `imp --help` to display help and version information and exit.

```shell
$ imp -h

# Or:

$ imp --help
```

Use the command `imp -v` to display version information and exit.

```shell
$ imp -v
```

## Command Format

```shell
imp username/password@ip:port [KEYWORD[=value1,value2,...,valueN]]
```

**username/password**

The database user (login user) executing the import command, the password, and the address (default is localhost). See the specific description for privilege requirements in different import modes.

**KEYWORD**

Command options:

- FILE: The name of the import file, which must be provided.
- Import mode:
  - FULL: Import the entire file, Value is a single value Y or N.
  - FROMUSER: User import mode command, Value is the username.
    - TOUSER: Change the imported user, Value is the username, must be specified under FROMUSER mode, can be omitted; if omitted, defaults to not changing the imported user.
  - TABLES: Table import mode command, Value is the table name.
  - Must choose one of the above modes for import; if no import mode is specified in the command, it defaults to FROMUSER.
- IGNORE: Ignore errors when creating objects, Value is a single value Y or N, can be omitted; if omitted, defaults to N.
- ROWS: Specifies whether to import table data, Value is a single value Y or N, can be omitted; if omitted, defaults to Y.
- DATA_ONLY: Specifies whether to only import table data, Value is a single value Y or N, can be omitted; if omitted, defaults to N.
- LOG_PATH: Specifies the path for the import log, Value is the path name, can be omitted; if omitted, no log file is generated.
- LOG_LEVEL: Specifies the log level for the import log, Value is [ERROR, WARN, INFO, DEBUG, TRACE], can be omitted; if omitted, defaults to INFO.
- TRUNCATE: Specifies whether to overwrite existing tables in the target database when the tables to be imported already exist in the import file, Value is a single value Y or N, can be omitted; if omitted, defaults to N.
- MOJIBAKE_REPLACE: Specifies whether to tolerate unrecognizable characters in the source data file. Value is a single value Y or N, can be omitted; if omitted, defaults to N, indicating that no tolerance for unrecognizable characters is applied — if the source byte stream contains characters not supported by the tool's specified character set, import may fail. <br />The tolerance mechanism for unrecognizable characters is as follows:  <br/>1. When character set conversion fails due to unrecognizable characters in the source byte stream, skip 1 byte from the source byte stream and add a `?` character to the target buffer.  <br/>2. If unrecognizable characters still exist, repeat the above step until the end of the source byte stream. 

***Example***

```shell
$ imp sales/sales@127.0.0.1:1688 FILE=export.owner.export FROMUSER=sales
```

## Command Options

The metadata file exported from the source YashanDB contains deployment configurations and information related to the table type `default_table_type`. When importing the metadata file using `imp`, the `default_table_type` parameter on the target YashanDB must match the information in the metadata file. You can check the system parameter configurations on both the source and target databases by executing `SHOW parameter DEFAULT_TABLE_TYPE;` in the database client.

### FULL Mode

The FULL mode is used to import all data from the file, with specific data based on the file. Import supports the following objects:

- User metadata
- All object metadata under the user
- Table data under the user
- All system privileges
- All object privileges
- All roles
- All auditing policies/enables
- All OUTLINE
- All PROFILE
- All scheduled jobs
- All LBAC related information

In YashanDB, PROFILE and LBAC-related information can only be imported in FULL mode.


The database user executing this mode of import must be created and have DBA role privileges. The meaning of the Value is:

- Y: Import all data from the entire file; it is required to use `sys` as the import operation user.
- N: Import the data under the logged-in user.

Before executing FULL mode import, ensure that a tablespace with the same name has already been created on the target YashanDB; if the imported objects already exist, those objects will be skipped and the subsequent import tasks will continue.

Before executing this mode of import, ensure that the LBAC switch is enabled; otherwise, the import of LBAC related information will fail. Refer to [YLS_ENFORCEMENT](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/YLS_ENFORCEMENT) for LBAC switch operations.


***Example***

```sql
-- Use the commands or tools provided by YashanDB to create a tablespace with the same name on the target database (this example uses the default USERS tablespace)

-- Perform full database import
$ imp sys/********@127.0.0.1:1688 FILE=export.full.dump FULL=Y

-- Count the information of the imported objects
SELECT COUNT(1) ts_count FROM dba_tablespaces;
             TS_COUNT 
--------------------- 
                    6
SELECT COUNT(1) user_count FROM dba_users;
           USER_COUNT 
--------------------- 
                    3
SELECT COUNT(1) table_count FROM dba_tables;
           TABLE_COUNT 
--------------------- 
                   89
SELECT COUNT(1) index_count FROM dba_indexes;
           INDEX_COUNT 
--------------------- 
                  134
-- Count sales user area table data
SELECT COUNT(1) sales_data FROM sales.area;
           SALES_DATA 
--------------------- 
                    5
-- Count sales0 user area table data
SELECT COUNT(1) sales0_data FROM sales0.area;
          SALES0_DATA 
--------------------- 
                    5
```

### FROMUSER Mode

FROMUSER mode is used to import objects and data for a specified user, including:

- All object metadata under the user
- Table data under the user
- Object privileges of the objects under the user

The Value for this mode command is the username, which can be multiple; if duplicate usernames are entered, it will only import once. If the specified user is not present in the data file, a warning will be prompted.

For the database user executing this mode of import, if the Value matches this user, i.e., importing data under this user, there are no privilege requirements; if the Value contains another user, i.e., importing data under another user, it is required to have DBA role privileges.

Before executing this mode of import, ensure that a tablespace with the same name has already been created on the target YashanDB; if the imported objects already exist, the import will fail.

If there are cross-user dependencies, and the dependent objects do not exist in the database, the import will not succeed.

***Example***

```sql
-- Use the commands or tools provided by YashanDB to create a tablespace with the same name on the target database (this example uses the default USERS tablespace)

-- Create user sales0 with DBA privilege, and normal user sales
CREATE USER sales0 IDENTIFIED BY sales0;
GRANT DBA TO sales0;
CREATE USER sales IDENTIFIED BY sales;
GRANT CREATE SESSION TO sales;

-- Import sales user data
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export FROMUSER=sales

-- Log in as sales user, count related data
SELECT COUNT(1) table_count FROM user_objects;
             COUNT(1) 
--------------------- 
                   30
SELECT COUNT(1) data_count FROM sales_info;
           DATA_COUNT 
--------------------- 
                    6
```

### TOUSER

The TOUSER command is used to change the user of imported objects, replacing the owner of the imported objects with the user specified by TOUSER.

The Value for this command is the username, which can be multiple; if duplicate usernames are entered, import will occur multiple times, with a default value of NONE.

The database user executing the TOUSER command must have DBA privileges.

The TOUSER command must be used in conjunction with the FROMUSER command, and the count of Values must remain consistent.

Before executing this command for import, ensure that a tablespace with the same name and the specified TOUSER has already been created on the target YashanDB; if the imported objects already exist, the import will fail.

***Example*** 

```sql
-- Create normal user sales1
CREATE USER sales1 IDENTIFIED BY sales1;
GRANT CREATE SESSION TO sales1;

-- Import sales user data into sales1 user
$ imp sales0/sales0@127.0.0.1:1688 file=export.owner.export fromuser=sales touser=sales1

-- The Value of FROMUSER will be deduplicated; the Value of TOUSER will not be deduplicated; ensure the deduplicated count of FROMUSER matches the count of TOUSER.
$ imp sales0/sales0 file=export.owner.export fromuser=sales,sales touser=sales1,sales1
YAS-08025, invalid value of parameter FROMUSER/TOUSER.
import terminated unsuccessfully
```

### TABLES Mode

TABLES mode is used to import specified table data, including:

- Table metadata
- Indexes, comments, constraints, column attributes related to the table
- Table data
- Table object privileges

The Value for this mode command is the table name, which can be multiple; if duplicate table names are entered, it will only import once. If the specified table is not present in the data file, a warning will be prompted.

For the database user executing this mode of import, if they are the owner of the imported table, there are no privilege requirements; otherwise, they must have DBA privileges and must specify the owner's table through FROMUSER.

Before executing this mode of import, ensure that the same database, tablespace, and user already exist; if the imported objects already exist, the import will fail.

If the imported table has dependencies, and the dependent objects do not exist in the database, the import will not succeed.

***Example***

```sql
-- Drop existing sales_info table
DROP TABLE IF EXISTS sales_info;

-- Execute table import
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.table.export TABLES=sales_info FROMUSER=sales

-- Log in as SALES user, count sales_info table-related data
SELECT COUNT(1) constriant_count 
FROM user_constraints 
WHERE table_name='SALES_INFO';
     CONSTRIANT_COUNT 
--------------------- 
                    4
SELECT COUNT(1) index_count
FROM user_indexes 
WHERE table_name='SALES_INFO';
          INDEX_COUNT 
--------------------- 
                    1
SELECT COUNT(1) part_count 
FROM user_tab_partitions 
WHERE table_name='SALES_INFO';
           PART_COUNT 
--------------------- 
                    3
SELECT COUNT(1) data_count FROM sales_info;
           DATA_COUNT 
--------------------- 
                    6
```

### IGNORE

During the *imp* import process, an operation to create objects will be executed, which may result in errors indicating that the objects to be created already exist. IGNORE is used to indicate how the system handles such errors:

- If IGNORE is Y: Indicates that the error can be ignored, and continues to import the relevant data content of that object.
- If IGNORE is N: Indicates that this error should not be ignored, outputs a warning that the object already exists, and skips the relevant data content of that object, moving on to the next object to process. The default value is N.

***Example***

```sql
-- Execute table import
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.table.export TABLES=sales_info FROMUSER=sales IGNORE=y
```

### ROWS

This option is used to specify whether to import table data; when set to Y, it means both table data and metadata will be imported; when set to N, it means only metadata will be imported, which can be omitted; if omitted, it defaults to Y.

### DATA\_ONLY

This option is used to specify whether to only import table data; when set to Y, it signifies that only table data will be imported without metadata (indexes, constraints, etc.); when set to N, it has no actual effect, can be omitted; if omitted, it defaults to N.

Ensure that ROWS=Y before specifying DATA_ONLY=Y, otherwise, an error will be returned.

***Example***

```shell
# Import only table data
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export FROMUSER=sales ROWS=Y DATA_ONLY=Y

# Import both metadata and table data
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export FROMUSER=sales ROWS=Y DATA_ONLY=N

# Import only metadata
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export FROMUSER=sales ROWS=N DATA_ONLY=N

# Return error
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export FROMUSER=sales ROWS=N DATA_ONLY=Y
```

### TRUNCATE

This option specifies whether to overwrite tables that already exist in the target database when tables in the import file are being imported (overwriting means automatically deleting the existing data first, then importing the corresponding data from the file), and can be omitted. This option does not take effect when only importing metadata.

- TRUNCATE=N means do not overwrite, default is N.
- TRUNCATE=Y means overwrite directly. If the tables to be imported have foreign key constraints, the truncate operation may fail; in that case, the failed tables will be skipped and the import operations will continue.
When TRUNCATE=Y is configured, IGNORE=Y will be set by default, meaning that if there are conflicts in metadata import, it will skip and continue with the import of table data and index constraints, etc. If IGNORED is set to N when TRUNCATE=Y is configured, an error will be returned.
When both TRUNCATE=Y and DATA_ONLY=Y are configured, it indicates that only the table data will be truncated and imported, skipping index constraints, etc.

***Example***

```shell
# Import metadata and table data, automatically ignore metadata conflicts
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export TRUNCATE=Y

# Import table data
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export ROWS=Y DATA_ONLY=Y TRUNCATE=Y

# Return error
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export IGNORE=N TRUNCATE=Y
```
