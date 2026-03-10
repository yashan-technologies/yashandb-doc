YashanDB provides compatibility functionality for MySQL databases. However, due to differences in underlying architectures and product shapes of different databases, certain features adapted for MySQL databases may not be compatible with those adapted for Oracle databases. YashanDB addresses this issue effectively through the use of toggle switches, allowing users to migrate from various databases to YashanDB without extensive SQL validation and rewriting.

YashanDB determines its compatibility with MySQL based on the chosen syntax mode, which users can select during database installation:

- [mysql mode](../../mysql模式参考手册/mysql模式概述): Applicable only to Standalone Deployment. In this mode, SQL statements entered by the user will be parsed according to MySQL's syntax, suitable for scenarios that require close compatibility with MySQL databases.
- yashan mode: In this mode, the syntax system of YashanDB is used. If specified as yashan mode during installation (defaulting to yashan mode if omitted), switching to mysql mode is not possible after installation. 


In mysql mode, SQL statements entered by the user will be parsed according to MySQL's syntax, and it also supports MySQL-specific statements, such as the show statement, use statement, etc.

## Control Switch

During the installation process of YashanDB, the mode parameter in the yasboot package se gen command can specify the syntax mode as mysql mode.

After installation in mysql mode, all sessions created during the database OPEN phase default to mysql mode. Sessions created during the NOMOUNT or MOUNT phases remain in yashan mode, and the current session's syntax mode can be viewed as follows:

```sql
-- Query the COMPAT_VECTOR parameter value
SHOW PARAMETER COMPAT_VECTOR

name                                                             value                                                  
---------------------------------------------------------------- ----------------------------------------------------------------
COMPAT_VECTOR                                                    mysql            
```

In mysql mode, certain database management operations (such as primary/standby switching) cannot be performed normally. Users need to switch the current session to yashan mode (without affecting other sessions) before executing statements/commands, and the switching method is as follows:

```sql
-- Switch to yashan mode, then perform relevant operational maintenance
ALTER SESSION SET COMPAT_VECTOR = yashan;

-- After maintenance operations are completed, switch back to mysql mode to continue business-related operations
ALTER SESSION SET COMPAT_VECTOR = mysql;
```

## Concept Introduction

|Term |yashan Mode (same as Oracle) |mysql Mode |
|--------------------|-----------------------|---------------|
| Database           | A complete instance consisting of tablespaces, data files, etc. <br/> One YashanDB environment corresponds to one Database. | A logical container for database objects (tables, views, etc.) |
| Schema             | Corresponds one-to-one with users, a logical container for user database objects (tables, views, etc.) <br/> Created automatically when a user is created with the same name schema. | An alias for a database  |
| User               | Login account + the schema owner with the same name (naturally has all privileges on objects under this schema) <br/> Can be granted privileges on other schema objects. | Only the login account<br/> Can be granted privileges on any database. |
| Role               | A collection of privileges | None<br/> You can use the built-in roles of YashanDB (which parse related SQL statements based on yashan syntax during use). |

## Specific Compatibility Items

### Communication Protocol Compatibility



When YashanDB is installed in mysql mode, a separate listening port is enabled by default to handle connections and execute protocol commands initiated to the YashanDB server via the MySQL protocol.



### Syntax Compatibility



In the syntax system of YashanDB, the SELECT statement must include a FROM clause, whereas MySQL's syntax allows SELECT statements without a FROM clause. After switching to mysql mode, executing the following statement will return the current session's login username without an error:

```SQL
SELECT USER();
```

- **Lexical**

    For example, in mysql mode, backticks (`) represent object names, single quotes (') represent strings, and double quotes (") default to representing strings, but when ANSI_QUOTES is included in sql_mode, they represent object names. In yashan mode, double quotes represent objects, and single quotes represent strings.

- **Concept of Objects**

    For example, the CREATE DATABASE statement in mysql mode represents the creation of a Schema.

- **Data Types**

    For example, the Bool type in mysql mode represents Tinyint(1).

- **Literal Data Types**

    Character literal data types in mysql mode are of type VARCHAR (compatible with MySQL), while in yashan mode, the type is CHAR (compatible with Oracle).

- **Functions**

    On the basis of the original YashanDB, over 100 MySQL functions with the same names have been synchronized; additionally, more than 80 built-in functions unique to MySQL have been implemented, covering time and date, character processing, mathematical operations, flow control, system information, password verification, and other functions.

- **Data Definition Language**
    
    For example, the CREATE TABLE statement in mysql mode will support options like Engine, Character set, etc., but will no longer support options like pctfree.

- **Data Manipulation Language**
    
    For example, in mysql mode, SELECT statements can be executed without a FROM clause.

- **Permissions**

    For example, executing the SELECT FOR UPDATE statement in mysql mode requires the user to have read permission on the table and at least one of the permissions for insert, delete, or update. In yashan mode, the SELECT statement requires read (READ) permission, while the SELECT FOR UPDATE statement requires separate SELECT permission.

- **Collation**

    In mysql mode, comparison and sorting of character types are affected by the collation (e.g., when the database character set is selected as UTF8MB4, the default collation is UTF8MB4_GENERAL_CI, where character type sorting and comparison are case-insensitive and ignore trailing spaces), whereas in yashan mode, they are compared and sorted in binary mode (case-sensitive, with trailing spaces included in comparison).

> **Caution**:
>
> - YashanDB 23.4 does not yet cover all MySQL syntax and behaviors. For statements that are not covered, they will still be parsed and executed according to YashanDB syntax when executed in mysql mode.
>
> - For functionalities of YashanDB that MySQL itself does not support, these types of statements will not produce ambiguity in mysql mode. Therefore, they can still be parsed and executed according to YashanDB syntax in mysql mode, such as set operations in query statements (MINUS, INTERSECTS), hierarchical queries (CONNECT BY).



### Reserved Words Compatibility



YashanDB is compatible with most reserved words of the MySQL database, but currently, there are still certain differences in some reserved words, including:

- CONNECT
- EXCEPT
- IF
- INTERSECT
- MINUS
- OF
- PUBLIC
- ROW
- ROWS
- START
- SYSDATE



## Specification Differences

In mysql mode, YashanDB maintains consistency with most specifications of yashan mode. The following table lists only those specifications that differ from yashan mode.



### Object Specifications

|Specification Name |Specification Type |Maximum Value |
| ------------------- | -------- | ------------------------------------------ |
| Number of users        | Maximum Value      | 10240 - Built-in user count - database/schema count |
| Number of databases/schemas | Maximum Value  | 10240 - Built-in user count - Ordinary user count |
| Maximum length of user name | Maximum Value  | 60Bytes                                           |

### Data Type Specifications

- Full compatibility with integer, floating-point, DECIMAL, BOOLEAN, date and time types, fixed-length/variable-length character/binary types, national character set types, BLOB/TEXT types and MySQL.

- Support for MySQL-specific unsigned types.

- New synchronization of float/double floating-point precision;

- Enhancement of character type specifications: maximum specification for character/binary types increased to 65534.

- Support for MySQL implicit conversion rules, synchronizing the implicit conversion logic between date and time types, character types, binary types, and floating-point types.



For more details, please refer to [Data Types (mysql mode)](../../mysql模式参考手册/SQL参考手册/数据类型/00数据类型).

## Functionality Constraints

When using mysql mode, the following constraints apply:

|Constraint Item |Constraint Behavior |
| ----------------------- |-------------------------------------------|
| Deployment Mode          | The mysql mode can only be selected for standalone (primary/standby) deployment.                                     |
| Table Type               | Only row storage tables can be created in mysql mode.         |
| User Login               | * Client must support SHA256 plugin if using users created in yashan mode to log in to YashanDB (mysql mode).<br/>* If using yasql to log in with users created in mysql mode, the username needs to be enclosed in double quotes.             |
| User Deletion and Modification | Deleting or modifying users created in yashan mode is not allowed in mysql mode.                            |
| Object Name              | The lower_case_table_names parameter only supports being set to 1.<br/> Schema, table, view, and table alias names are displayed in lowercase.<br/> Object name matching is case-insensitive.          |
| Binary/Blob Type Text Protocol | When there is a character set inconsistency between the client and server, transcoding will occur based on the server's character set, which may differ from client expectations.                        |
| Character Set            | The character set at schema/table/column level must be consistent with the instance-level character set setting.          |
| Collation                | Character collation can be specified at schema/table/column level and supports actual functionality, but must be compatible with the instance-level character set.                |
| Global Variables          | Queries and settings for global variables are only for syntax compatibility; most will not take effect except for character set and auto-commit.       |
| SQL_MODE                | In YashanDB mysql mode, the SQL_MODE syntax switches such as ANSI_QUOTES, NO_AUTO_VALUE_ON_ZERO, and NO_BACKSLASH_ESCAPES and so on are implemented, and you can refer to [SQL_MODE](../../mysql模式参考手册/SQL参考手册/SQL_MODE) to view the detail. However, for all other configuration switches, regardless of whether they are set or not, they will not affect database behavior — the actual execution behavior remains consistent with the yashan mode. |
| Executable Comments       | Executable comments are treated as comments in mysql mode and have no effect on statement execution.                    |
