## Command Format

```shell
exp username/password@ip:port [KEYWORD[=value1,value2,...,valueN]]
```

**username/password@ip:port**

The password for the database user (login user) executing the export command, along with the server address. For privilege requirements, see the specific descriptions for different export modes.

**KEYWORD**

Command options:

- FILE: The name of the export file. Must be provided and will overwrite existing files.
- Export modes are as follows; only one mode can be selected for each export. If no export mode is specified in the command, the default is FULL=N.
  - FULL: Command for exporting the entire database. Value can be a single Y or N.
  - OWNER: Command for exporting user objects. Value is the username.
  - TABLES: Command for exporting specific tables. Value is the table name.
- ROWS: Specifies whether to export table data. Value can be a single Y or N, optional; default is Y if omitted.
- LOG_PATH: Specifies the path for the export log. Value is the pathname, optional; if omitted, no log file will be generated.
- LOG_LEVEL: Specifies the log level for the export log. Value can be [ERROR, WARN, INFO, DEBUG, TRACE], optional; default is INFO if omitted.

***Example***

```shell
$ exp sales/sales@127.0.0.1:1688 FILE=export.full.dump FULL=Y
```

## Command Options

The exported metadata file contains information related to deployment structure and table types. When importing this file, the deployment structure and default_table_type of the database being imported must match the information in the file, except for files of standalone HEAP tables and YAC HEAP tables, which can be interchanged.

### FULL Mode

FULL mode is used to export all data from the database, including:

- User metadata
- Metadata for all objects under the user
- Table data under the user
- All system privileges 
- All object privileges 
- All roles
- All audit policies/enabled settings
- All OUTLINEs
- All PROFILEs
- All SQLMAPs
- All scheduled jobs
- All LBAC related information

In YashanDB, PROFILE and LBAC-related information is only supported for export in FULL mode.


The database user executing this mode must have DBA role privileges. The meaning of the Value is as follows:

- Y: Export data for all users.
- N: Export data for the logged-in user only.


***Example***

```sql
-- SALES is a user with DBA privilege
-- Count object information before export
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
-- Count sales.user data for the area table
SELECT COUNT(1) sales_data FROM sales.area;
           SALES_DATA 
--------------------- 
                    5
-- Count sales0.user data for the area table
SELECT COUNT(1) sales0_data FROM sales0.area;
          SALES0_DATA 
--------------------- 
                    5

-- Execute full database export
$ exp sales/sales FILE=export.full.dump FULL=Y
```

### OWNER Mode

OWNER mode is used to export objects and data for a specified user, including:

- Metadata for all objects under the user
- Data for the user's tables
- Object privileges for user's objects 

The Value for this mode is the username, which can be multiple, and repeated usernames will export only once. Up to 1024 different users can be specified.

For the database user executing the export in this mode, if the Value matches this user, meaning exporting data of this user, there are no privilege requirements; if the Value includes other users, the user must have DBA role privileges.

Regarding cross-user object dependencies, assume users A and B have dependency relationships. If only data from user A is exported and the objects of user B do not exist in the database during import, the import operation will fail.

***Example***

```sql
-- Log in as SALES user and count related data
SELECT COUNT(1) table_count FROM user_objects;
             COUNT(1) 
--------------------- 
                   30
SELECT COUNT(1) data_count FROM sales_info;
           DATA_COUNT 
--------------------- 
                    6

-- Execute export
$ exp sales/sales FILE=export.owner.export OWNER=sales
```

### TABLES Mode

TABLES mode is used to export data from specified tables, including:

- Table metadata
- Information related to indexes, comments, constraints, and column properties of the table
- Table data
- Object privileges of the table 

The Value for this mode is the table name, which can be multiple, and repeated table names will export only once. Up to 1024 different tables can be specified.

For the database user executing the export in this mode, if they are the OWNER of the exported table, there are no privilege requirements; otherwise, they must have DBA role privileges.

***Example***

```sql
-- Log in as SALES user and count data for the sales_info table
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
                    
-- Execute export
$ exp sales/sales FILE=export.table.export TABLES=sales_info
```

### ROWS Mode

This option specifies whether to export table data. Y indicates exporting both metadata and table data, while N indicates exporting only metadata. This option is optional; if omitted, the default is Y.

***Example***

```shell
# Export both table data and metadata
$ exp sales/sales@127.0.0.1:1688 FILE=export.full.dump FULL=Y ROWS=Y

# Export only metadata
$ exp sales/sales@127.0.0.1:1688 FILE=export.full.dump FULL=Y ROWS=N
```

> **Note**: 
>
> When exporting index data, if the index owner is different from the table owner, the index owner will automatically change to the table owner after export, ensuring that the index can be imported normally using the fromuser mode without affecting database performance.