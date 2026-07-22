## Data Dictionary Types

The data dictionary refers to a set of system tables, system views, and dynamic views used by YashanDB to manage and display database metadata. It stores metadata for objects at all levels within the database, which may be utilized in various scenarios during database operation.

For example, when the YashanDB client tool or user program connects to the database via the YashanDB driver, the database will look up the identity information (username, password, role, etc.) for the user attempting to log in from the relevant data dictionary. It will also check the privilege related to the user in the privilege-related data dictionary to determine whether the user has the privilege to create a session.

When a user executes an SQL statement, such as:
```sql
select id, name from employees where id < 100
```

the database's syntax parser will analyze the statement, identifying that it tries to query the employees object to retrieve the values of the id and name fields. Additionally, the database needs to confirm the following information through the data dictionary:

- Whether the employees object is stored and whether its type is a table or a view.

- Whether the current user has select or read privilege on the employees object.

- Whether id and name are columns in the employees table.

- The data types and lengths of the id and name columns.

- Whether the id column of the employees table has an index.

- The statistics of the employees table.

### System Tables

System tables are automatically maintained by YashanDB. Typically, changes in system tables occur when a user executes data definition language (DDL) statements. The information recorded in system tables includes key metadata about the database and its objects. Their table structure and field meanings are closely tied to the internal mechanisms of YashanDB, so it is not recommended for database administrators to modify the data in system tables themselves.

### System Views

System views are a set of views created based on system tables that present metadata of interest to users in an understandable way.

The following table lists three common groups of system views:

|View Name Prefix |Users |Display Content |
| ------------ | ------------ | ------------------------------------ |
| DBA_             | Database Administrators | Metadata of all objects in the database             |
| ALL_             | All Users        | Metadata of all objects accessible to the current user |
| USER_            | All Users        | Metadata of all objects belonging to the current schema  |

#### DBA Views

Views starting with DBA_ allow users with DBA privilege to view all objects in the database.

For example, to view all objects of all users in the database:

```sql
SELECT OWNER, OBJECT_TYPE, OBJECT_NAME FROM DBA_OBJECTS ORDER BY 1, 2, 3;
```

#### ALL Views

Views starting with ALL_ allow all users to view the metadata of database objects that they have access privilege to, i.e., metadata of all objects visible from the user's perspective.

The objects visible in ALL_ views include:

- All objects belonging to the schema.
- Objects that other users have granted access privilege to the user, including those granted to PUBLIC by other users.
- All objects in the PUBLIC schema.

For example, to view all accessible objects in the database:

```sql
SELECT OWNER, OBJECT_TYPE, OBJECT_NAME FROM ALL_OBJECTS ORDER BY 1, 2, 3;
```

#### USER Views

Views starting with USER_ allow all users to view metadata of objects owned by themselves.

The data returned by USER views is typically a subset of that from ALL views. Compared to ALL views, USER views generally do not have the OWNER column.

### Dynamic Views

Dynamic views present key metadata, not recorded in system tables, about the instance, database operation phase, etc., in a tabular format through internal mechanisms.

For example, the V$DATABASE view:

```sql
select owner, object_type from dba_objects where object_name='V$DATABASE';

OWNER                                                            OBJECT_TYPE           
---------------------------------------------------------------- --------------------- 
PUBLIC                                                           SYNONYM    

select table_owner, table_name from dba_synonyms where synonym_name='V$DATABASE';

TABLE_OWNER                                                      TABLE_NAME                                                       
---------------------------------------------------------------- ---------------------------------------------------------------- 
SYS                                                              V_$DATABASE       
```

Through the system views, it can be seen that V$DATABASE is a public synonym for SYS.V_$DATABASE.

```sql
select owner, text from dba_views where view_name='V_$DATABASE'; 

OWNER                                                            TEXT                                                             
---------------------------------------------------------------- --------------------------------------------------------------- 
SYS                                                              select "DATABASE_ID","DATABASE_NAME","CREATE_TIME","LOG_MODE","OPEN_MODE","PROTECTION_MODE","PROTECTION_LEVEL","DATABASE_ROLE","BLOCK_SIZE","CURRENT_SCN","STATUS","RCY_POINT","FLUSH_POINT","RESET_POINT","PLATFORM_NAME","HOST_NAME","RESTORE_TIME","SWITCHOVER_STATUS","SUPPLEMENTAL_LOG_DATA_MIN","SUPPLEMENTAL_LOG_DATA_PK","SUPPLEMENTAL_LOG_DATA_ALL","SUPPLEMENTAL_LOG_TABLE_TYPE" from v$database
```

From the definition, it can be seen that V_$DATABASE is a view defined by the SYS user, with its data sourced from the v$database object in the SYS schema. The v$database is a dynamic view that is not recorded as a schema object in system tables; however, its definition will be loaded into memory during instance startup and database loading.

Users can view all dynamic views by querying the V$DYNAMIC_VIEWS view.

## Dictionary Cache

During database operation, frequent access to the data dictionary may be required, especially for user account and schema object related metadata stored in system tables. If the data can only be retrieved by scanning system tables each time, it would significantly impact the system's response time.

YashanDB supports a dictionary cache that caches critical metadata in memory using specific data structures. Based on usage frequency, quantity, and memory footprint, the dictionary cache can be categorized into resident buffers and dynamic buffers.

### Resident Buffer

For system objects that are frequently used, have relatively small data volumes, and occupy less memory, the buffer remains resident in memory once loaded, such as user account information.

### Dynamic Buffer

For objects that are numerous and occupy considerable memory, a buffer is dynamically generated only during usage, such as table metadata. When buffer memory is insufficient, some temporarily unused object buffers may be evicted.