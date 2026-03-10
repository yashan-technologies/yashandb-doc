YashanD provides compatibility features for MySQL databases. However, due to differences in underlying architectures and product forms of different databases, some features that are compatible with MySQL database cannot be compatible with certain features compatible with Oracle database. YashanDB effectively addresses this issue through the use of control switches, allowing users to migrate from various databases to YashanDB without extensive SQL verification and rewriting.

## Specification Differences

When YashanDB is deployed in mysql mode, most specifications are consistent with yashan mode, with only the specifications listed in the table below differing from those in yashan mode. For detailed specification parameters, please refer to [Product Specifications](../Product Overview/Specifications/00Specifications).



### Object Specifications

|Specification Name |Specification Type |Maximum Value |
| ------------------- | -------- | ------------------------------------------ |
| Number of users        | Maximum Value      | 10240 - Built-in user count - database/schema count |
| Number of databases/schemas | Maximum Value  | 10240 - Built-in user count - Regular user count |
| Maximum length of user name | Maximum Value  | 60Bytes                                           |

### Data Type Specifications

- Full compatibility with integer, floating-point, DECIMAL, BOOLEAN, date and time types, fixed-length/variable-length character/binary types, national character set types, BLOB/TEXT types and MySQL.

- Support for MySQL-specific unsigned types.

- New synchronization of float/double floating-point precision;

- Enhancement of character type specifications: maximum specification for character/binary types increased to 65534.

- Support for MySQL implicit conversion rules, synchronizing the implicit conversion logic between date and time types, character types, binary types, and floating-point types.



For more details, please refer to [Data Types in mysql mode](./SQL Reference/Data Types/00Data Types).

## Character Set and Collation

- Full compatibility with five database-level character sets including ASCII, GBK, UTF8, UTF8MB4, GB18030.

- New column-level collate, supporting latin1_bin, utf8mb4_bin, utf8mb4_general_ci.

- Support for MySQL case sensitivity synchronization, SQL metadata retrieval, and protocol return of table/database names with consistent case sensitivity with MySQL, supporting setting lower_case_table_name to 1.

## Communication Protocol Compatibility



When YashanDB is installed in mysql mode, a separate listening port is enabled by default to handle connections and execute protocol commands initiated to the YashanDB server via the MySQL protocol.



## Syntax Compatibility



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



## Operations and Maintenance Compatibility

Support has been provided for INFORMATION_SCHEMA, PERFORMANCE_SCHEMA, and three types of MySQL maintenance views.

## Reserved Word Compatibility



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



## Ecological Compatibility

In mysql mode, YashanDB supports the following versioned ecological tools to connect and operate on YashanDB.

|Client/Tool |Version |Key Compatibility |
|------------------|-----------|---------------|
| mysql-jdbc         | 5.1.40      | - Full interface support<br/>- Automatic reconnection, batch processing, etc.  |
| mysqldump          | 10.13       | - Whole library/specified library/specified table data backup<br/>- 30+ parameter support |
| mysql_client       | 5.7.42      | - Full interface support<br/>- Batch processing, etc.   |
| mydump             | v0.16.9-1   | - Single table export<br/>- Concurrent export     |
| Navicat            | 10, 11      | - Command line SQL operations<br/>- Result set output<br/>- Metadata display  |
