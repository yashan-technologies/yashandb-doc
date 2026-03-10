YashanDB provides compatibility functionality for MySQL databases. However, due to differences in underlying architectures and product shapes of different databases, certain features adapted for MySQL databases may not be compatible with those adapted for Oracle databases. YashanDB addresses this issue effectively through the use of toggle switches, allowing users to migrate from various databases to YashanDB without extensive SQL validation and rewriting.

YashanDB determines its compatibility with MySQL based on the chosen syntax mode, which users can select during database installation:

- [mysql mode](#COMPAT_VECTOR): Applicable only to Standalone Deployment. In this mode, SQL statements entered by the user will be parsed according to MySQL's syntax, suitable for scenarios that require close compatibility with MySQL databases.
- yashan mode: In this mode, the syntax system of YashanDB is used. If specified as yashan mode during installation (defaulting to yashan mode if omitted), switching to mysql mode is not possible after installation. At this point, only a portion of MySQL-specific syntax supported by extension can be used, suitable for scenarios that occasionally need to adapt to MySQL databases. For detailed information, please refer to [yashan mode compatibility with certain MySQL syntax](#SQL_PLUGIN).

<span id="COMPAT_VECTOR" name="COMPAT_VECTOR" class="yaslink"></span>

## mysql mode

In mysql mode, SQL statements entered by the user will be parsed according to MySQL's syntax, and it also supports MySQL-specific statements, such as the show statement, use statement, etc.

### Control Switch

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

### Concept Introduction

|Term |yashan Mode (same as Oracle) |mysql Mode |
|--------------------|-----------------------|---------------|
| Database           | A complete instance consisting of tablespaces, data files, etc. <br/> One YashanDB environment corresponds to one Database. | A logical container for database objects (tables, views, etc.) |
| Schema             | Corresponds one-to-one with users, a logical container for user database objects (tables, views, etc.) <br/> Created automatically when a user is created with the same name schema. | An alias for a database  |
| User               | Login account + the schema owner with the same name (naturally has all privileges on objects under this schema) <br/> Can be granted privileges on other schema objects. | Only the login account<br/> Can be granted privileges on any database. |
| Role               | A collection of privileges | None<br/> You can use the built-in roles of YashanDB (which parse related SQL statements based on yashan syntax during use). |

### Specific Compatibility Items

#### Communication Protocol Compatibility

When YashanDB database management system is installed in mysql mode, a separate listening port will be enabled by default to handle connections and execute protocol commands initiated by the MySQL protocol to the YashanDB server.

#### Syntax Compatibility

In YashanDB's syntax system, the SELECT statement must include a FROM clause, whereas MySQL's syntax allows SELECT statements without a FROM clause. When switched to mysql mode, executing the following statement will return the current session's login username instead of reporting an error:

```SQL
SELECT USER();
```

When the session's syntax mode is switched to MySQL, syntax parsing and statement execution will follow the style of MySQL 5.7. The following differences may exist compared to YashanDB:

- **Lexical**

    For example, in mysql mode, backticks (`) denote object names, single quotes (') denote strings, and double quotes (") by default denote strings, but when ANSI_QUOTES is included in sql_mode, they denote object names. In yashan mode, double quotes denote objects, and single quotes denote strings.

- **Object Concept**

    For instance, the CREATE DATABASE statement in mysql mode represents creating a schema.

- **Data Types**

    For example, the Bool type in mysql mode denotes Tinyint(1).

- **Literal Data Types**

    For character type literals, in mysql mode its data type is VARCHAR (compatible with MySQL), while in Yashan mode, the type is CHAR (compatible with Oracle).

- **Data Definition Language**
    
    For example, the CREATE TABLE statement in mysql mode will be compatible with options such as Engine, Character set, etc., but will not support options like pctfree.

- **Data Manipulation Language**
    
    For example, mysql mode supports SELECT statements without a FROM clause.

- **Privileges**

    For instance, executing SELECT FOR UPDATE statement in mysql mode requires the user to have read privileges on the table, as well as any one of insert, delete, or update privileges. In yashan mode, the SELECT statement requires read (READ) privilege, while the SELECT FOR UPDATE statement requires separate SELECT privilege.

- **Character Collation**

    In mysql mode, character type comparisons and sorting rules are influenced by character collation (for example, when the database character set is selected as UTF8MB4, the default character collation is UTF8MB$_GENERAL_CI, where character type sorting and comparison are case-insensitive and ignore trailing spaces), whereas in Yashan mode, comparisons and sorting are done in a binary manner (case-sensitive and trailing spaces are included in comparisons).

> **Caution**:
>
> - YashanDB 23.4 has not yet covered all MySQL syntax and behavior. For statements not yet covered, executing in mysql mode will still parse and execute according to YashanDB syntax.
>
> - For functionalities not supported by MySQL, such statements will not cause ambiguity in mysql mode, so they can still be parsed and executed according to YashanDB syntax in mysql mode, such as set operations in query statements (MINUS, INTERSECTS) and hierarchical queries (CONNECT BY).

#### Reserved Words Compatibility

YashanDB is compatible with most reserved words of the MySQL database, but currently, there are still some reserved words that have certain differences, including:

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

### Specification Differences

In mysql mode, YashanDB maintains consistency with most specifications of yashan mode. The following table lists only those specifications that differ from yashan mode.

#### Object Specifications

|Specification Name |Specification Type |Maximum Value |
| ------------------- | -------- | ------------------------------------------ |
| user quantity            | Maximum Value      | 10240 - number of built-in users - number of databases/schemas |
| database/schema quantity  | Maximum Value      | 10240 - number of built-in users - number of normal users |
| user name length         | Maximum Value      | 60Bytes                                           |

#### Data Type Specifications

For details, please refer to [Data Types (mysql mode)](../../Development Guide/SQL Reference Manual/Data Types (mysql Mode)/00Data Types (mysql Mode)).

### Functionality Constraints

When using mysql mode, the following constraints apply:

|Constraint Item |Constraint Behavior |
| ----------------------- |-----------------------------------------------------------------------------------------------------------------------------------------------|
| Deployment Type          | Only Standalone Deployment can be deployed in mysql mode.                                                                                                                                           |
| Table Types              | Only heap table creation is supported in mysql mode.                                                                                                                            |
| User Login               | * To log in to YashanDB (mysql mode) using a user created in yashan mode, the client must support the SHA256 plugin. <br /> * When using *yasql* to log in to YashanDB (mysql mode) with a user created in mysql mode, the username must be surrounded by double quotes.  |
| User Deletion and Modification | Deleting and modifying users created in yashan mode is not allowed in mysql mode.                                                                                                         |
| Object Names             | The lower_case_table_names parameter can only be set to 1.<br/> The names of schemas, tables, views, and table aliases will be displayed in lowercase, while column names will be displayed in uppercase.<br/> Object name matching is case-insensitive. |
| Binary/Blob Type Text Protocol | When the character set of the client and server does not match, it will be transcoded according to the server character set, which may not align with client expectations.                                       |
| Character Set            | Only instance-level character set settings are supported; schema/table/column-level character sets are only for syntax compatibility.                                                                           |
| Collation                | Only instance-level collation settings are supported; schema/table/column-level collations are only for syntax compatibility.                                                                          |
| Global Variables         | Global variable querying and setting are only for syntax compatibility; except for character set and autocommit, most will not take effect practically.                                                           |
| SQL_MODE                 | Other SQL_MODE settings will not affect database behavior, and the actual execution effects remain consistent with yashan mode, apart from ANSI_QUOTES, NO_BACKSLASH_ESCAPES, PIPES_AS_CONCAT, REAL_AS_FLOAT, and PAD_CHAR_TO_FULL_LENGTH settings.  |
| Executable Comments      | Executable comments will be treated as comments in mysql mode and will not affect statement execution.                                                                 |

<span id="SQL_PLUGIN" name="SQL_PLUGIN" class="yaslink"></span>

## yashan mode Compatibility with MySQL Syntax

Yashan mode is the default syntax mode of YashanDB. If specified as yashan mode during installation (defaulting to yashan mode if omitted), switching to mysql mode is not possible after installation; at this point, only a portion of MySQL-specific syntax supported by extension can be used.

### Control Switch

In yashan mode, enabling compatibility with MySQL syntax allows convenient use of functionalities unavailable in Oracle but available in MySQL. When not enabled, only conflicting functionalities are unavailable; other normal functionalities are unaffected.

YashanDB uses the configuration parameter SQL_PLUGIN to control whether to enable the MySQL compatibility switch, with the default value set to NONE, meaning that MySQL compatibility is turned off by default. Setting the parameter value to MySQL indicates enabling MySQL syntax compatibility.

```sql
-- Enable MySQL syntax compatibility
ALTER SYSTEM SET SQL_PLUGIN = 'MYSQL';
or
ALTER SYSTEM SET SQL_PLUGIN = 'MYSQL' SCOPE = MEMORY;
or
ALTER SYSTEM SET SQL_PLUGIN = 'MYSQL' SCOPE = SPFILE;

-- Disable MySQL syntax compatibility
ALTER SYSTEM SET SQL_PLUGIN = 'NONE';
or
ALTER SYSTEM SET SQL_PLUGIN = 'NONE' SCOPE = MEMORY;
or
ALTER SYSTEM SET SQL_PLUGIN = 'NONE' SCOPE = SPFILE;
```

Here, SCOPE=MEMORY indicates that the configuration is effective only during the current instance operation; SCOPE=SPFILE indicates that the configuration does not take effect while the current instance is running, but takes effect after a restart; not specifying SCOPE means the configuration takes effect immediately and remains effective after the instance restarts.

### Impact Scope

After the MySQL syntax compatibility switch is enabled in yashan mode, the following changes will occur in the system:

|Feature |Before Enabling |After Enabling |
| --------------------------- | ------ | ------ |
| Multi-table DELETE                    | Not Supported     | Supported       |
| Multi-table UPDATE                    | Not Supported     | Supported       |
| DELETE table syntax                   | Supported         | Not Supported   |
| DELETE table FROM table syntax        | Not Supported     | Supported       |

***Example*** for Standalone Deployment Heap tables

```sql
-- employees is a table containing employee information with the following five records
SELECT BRANCH,DEPARTMENT,EMPLOYEE_NO,EMPLOYEE_NAME,SEX,ENTRY_DATE FROM employees;
BRANCH DEPARTMENT EMPLOYEE_NO   EMPLOYEE_NAME SEX   ENTRY_DATE                       
------ ---------- ------------- ------------- ----- -------------------------------- 
0101   000        0101000001    Mask          1     2020-09-09 22:55:32                   
0101   000        0101000002    John          1     2017-12-14 22:55:32                   
0201   010        0201010011    Anna          0     2022-08-10 22:55:32                   
0201   008        0201008003    Jack          1     2021-07-06 22:55:32                   
0101   008        0201008004    Jim           1     2022-11-18 22:55:32                  
 
-- Create a structure identical to employees in employees2
DROP TABLE IF EXISTS employees2;
CREATE TABLE employees2 AS SELECT * FROM employees WHERE 1=2;
INSERT INTO employees2 VALUES ('0101','008','0201008003','Jim','0',DATE '2021-11-17');
INSERT INTO employees2 VALUES ('0101','000','0101000002','John','1',DATE '2021-11-17');
COMMIT;
 
-- Before enabling
UPDATE employees,employees2 SET EMPLOYEES.EMPLOYEE_NAME = 'TOM',EMPLOYEES2.EMPLOYEE_NAME = 'TOM' WHERE EMPLOYEES.SEX = EMPLOYEES2.SEX;
YAS-04344 multi-table update is not supported
 
DELETE FROM employees,employees2;
YAS-04345 multi-table delete is not supported
 
DELETE employees;
 
DELETE employees FROM employees;
[1:18]YAS-04209 unexpected word FROM
 
-- After enabling
ROLLBACK;
ALTER SYSTEM SET SQL_PLUGIN = 'MYSQL' SCOPE = MEMORY;
 
UPDATE employees,employees2 SET EMPLOYEES.EMPLOYEE_NAME = 'TOM',EMPLOYEES2.EMPLOYEE_NAME = 'TOM' WHERE EMPLOYEES.SEX = EMPLOYEES2.SEX;
 
DELETE FROM employees,employees2;
 
DELETE employees;
[1:17]YAS-04209 unexpected word employees
 
DELETE employees FROM employees;
```

### Specific Compatibility Items

This document will elaborate on the compatibility of YashanDB's yashan mode with MySQL databases from the following aspects in Standalone Deployment and row storage:

- SQL syntax
- Expression operations
- FILTER CONDITION
- Data types
- Built-in functions
- PL
- System views
- Character sets
- SQL engine
- Database security
- Reserved words

#### SQL Syntax

YashanDB's yashan mode supports the mainstream SQL syntax in MySQL databases. Other minor incompatibilities due to functionality deficiencies will report unsupported syntax errors; in such cases, please contact our technical support for alternative solutions.

**(1) DML Type**

**SELECT**

- Supports most querying functionalities, including single-table queries, multi-table queries, subqueries, inner joins, semi-joins, outer joins, grouping and aggregation, hierarchical queries, etc.
- Supports set operations such as UNION and UNION ALL.
- Supports EXPLAIN statement to view execution plans.
- Supports random sampling query capabilities.

**INSERT**

- Supports single-row and multi-row inserts, as well as inserting into specified partitions.
- Supports INSERT INTO SELECT statements.
- Supports single-table inserts.
- Supports INSERT ON DUPLICATE KEY UPDATE syntax.

**UPDATE**

- Supports single column and multi-column updates.
- Supports the use of subqueries.

**DELETE**

- Supports single-table and multi-table deletes.
- Supports the use of subqueries.

**(2) DDL Type**

**HEAP Tables**

- CREATE TABLE/CREATE TABLE AS
- ALTER TABLE
- DROP TABLE
- TRUNCATE TABLE

**Temporary Tables**

- Supports creation and deletion of temporary tables.

**Partition Tables**

- Supports RANGE, LIST, HASH partitions.
- Supports ADD|DROP|TRUNCATE PARTITION.

**Constraints**

- Includes in-line constraints and out-of-line constraints.
- UNIQUE, PRIMARY KEY, FOREIGN KEY, CHECK, and (NOT) NULL constraints.

**Views**

- CREATE VIEW
- DROP VIEW
- Views support SELECT/INSERT/UPDATE/DELETE.

**BTree Indexes**

- Includes both unique and non-unique indexes.

#### Expression Operations

YashanDB's yashan mode implements mainstream computational framework capabilities for expression operations, detailed as follows:

|Expression Operation Type |YashanDB |MySQL |
| :------------- | :------- | :---- |
| Binary Addition           | Supported | Supported |
| Binary Subtraction        | Supported | Supported |
| Binary Multiplication      | Supported | Supported |
| Binary Division           | Supported | Supported |
| Binary Modulus           | Supported | Supported |
| Unary Negation            | Supported | Supported |
| Bitwise AND               | Supported | Supported |
| Bitwise OR                | Supported | Supported |
| Bitwise XOR               | Supported | Supported |

#### FILTER CONDITION

In YashanDB's yashan mode, FILTER CONDITION types are fully compatible with those of MySQL databases, except for ROWNUM, detailed as follows:

| filter               | YashanDB | MySQL  |
| :------------------- | :------- | :----- |
| ALL                     | Supported | Supported |
| AND                     | Supported | Supported |
| ANY                     | Supported | Supported |
| BETWEEN...AND...        | Supported | Supported |
| EXISTS                  | Supported | Supported |
| GREATER EQUAL           | Supported | Supported |
| GREATER                 | Supported | Supported |
| IN                      | Supported | Supported |
| IS NOT NULL             | Supported | Supported |
| IS NULL                 | Supported | Supported |
| LESS                    | Supported | Supported |
| LESS EQUAL              | Supported | Supported |
| LIKE                    | Supported | Supported |
| NOT                     | Supported | Supported |
| NOT BETWEEN...AND...    | Supported | Supported |
| NOT EQUAL               | Supported | Supported |
| NOT EXISTS              | Supported | Supported |
| NOT IN                  | Supported | Supported |
| NOT LIKE                | Supported | Supported |
| NOT RLIKE               | Supported | Supported |
| OR                      | Supported | Supported |
| EQUAL                   | Supported | Supported |
| RLIKE                   | Supported | Supported |
| ROWNUM                  | Supported | Not Supported |
| SOME                    | Supported | Supported |

#### Data Types

YashanDB's yashan mode implements the following comparison of data types with MySQL databases:

|Data Type |YashanDB |MySQL |
| :--------------------- | :------- | :----- |
| BOOLEAN             | Supported | Supported |
| TINYINT             | Supported | Supported |
| SMALLINT            | Supported | Supported |
| MEDIUMINT           | Not Supported | Supported |
| INTEGER             | Supported | Supported |
| BIGINT              | Supported | Supported |
| FLOAT               | Supported | Supported |
| DOUBLE              | Supported | Supported |
| NUMBER              | Supported | Not Supported |
| DECIMAL / NUMERIC   | Not Supported | Supported |
| DATE                | Supported | Supported |
| DATETIME            | Not Supported | Supported |
| TIMESTAMP           | Supported | Supported |
| TIME                | Supported | Supported |
| YEAR                | Not Supported | Supported |
| INTERVAL YEAR TO MONTH | Supported | Not Supported |
| INTERVAL DAY TO SECOND | Supported | Not Supported |
| CHAR                | Supported | Supported |
| VARCHAR             | Supported | Supported |
| RAW                 | Supported | Not Supported |
| CLOB                | Supported | Not Supported |
| TINYBLOB           | Not Supported | Supported |
| BLOB                | Supported | Supported |
| MEDIUMBLOB         | Not Supported | Supported |
| TINYTEXT           | Not Supported | Supported |
| TEXT                | Not Supported | Supported |
| LONGTEXT           | Not Supported | Supported |
| BIT                 | Not Supported | Supported |
| ROWID               | Supported | Not Supported |
| JSON                | Supported | Supported |

#### Built-in Functions

A comparison of built-in functions implemented in YashanDB's yashan mode with those of MySQL databases is shown as follows:

|Function Type |Supported Function List |YashanDB |MySQL |
| :-------------------- | :----------------------------------------------------------- | :------- | :----- |
| Mathematical Functions       | ABS, ACOS, ASIN, ATAN, ATAN2, AVG, CEIL/CEILING, COS, COT, DIV, FLOOR, MOD, PI, POW/POWER, RANDOM/RAND, SIGN, SIN, SQRT, TAN, TRUNCATE/TRUNC | Supported | Supported |
| String Functions             | ASCII, BIT_LENGTH, CHR/CHALEASTR, CHAR_LENGTH/CHARACTER_LENGTH, CONCAT, CONCAT_WS, FIND_IN_SET, GROUP_CONCAT, INSTR, LCASE/LOWER, LEFT, LENGTH, LPAD, LTRIM, POSITION, OCTET_LENGTH, RIGHT, RPAD, RTRIM, REPLACE, SUBSTR, SUBSTRING, SUBSTRING_INDEX, TRIM, UCASE/UPPER | Supported | Supported |
| Regex Functions              | REGEXP_LIKE, REGEXP_REPLACE, REGEXP_INSTR, REGEXP_SUBSTR     | Supported | Supported |
| Conversion Functions         | BIN, CAST                                                    | Supported | Supported |
| Aggregate Functions          | AVG, COUNT, GROUP_CONCAT, MAX, MIN, STDDEV, STDDEV_POP, STDDEV_SAMP, SUM, VAR_POP, VAR_SAMP, VAR_SAMP, VARIANCE | Supported | Supported |
| Window Functions             | FIRST_VALUE, LAG, LAST_VALUE, LEAD, RANK, ROW_NUMBER         | Supported | Supported |
| Date/Time Functions          | CURRENT_TIMESTAMP, DATE, DAYOFWEEK, DATE_FORMAT, DATE_ADD, DATE_SUB, EXTRACT, LAST_DAY, LOCALTIMESTAMP, NOW, SYSDATE, TIME, TIMESTAMP, TIMEDIFF, TIMESTAMPDIFF, UTC_TIMESTAMP | Supported | Supported |
| Conditional Functions         | CASE, IF, IFNULL                                             | Supported | Supported |
| JSON Functions                 | JSON, JSON_ARRAY_GET, JSON_ARRAY_LENGTH, JSON_EXISTS, JSON_FORMAT, JSON_PARSE, JSON_QUERY, JSON_SERIALIZE | Supported | Not Supported |
| MySQL Info Functions         | BENCHMARK, CHARSET, COERCIBILITY, COLLATION, CONNECTION_ID, CURRENT_ROLE, CURRENT_USER, DATABASE, ICU_VERSION, ROLES_GRAPHML, ROW_COUNT, SCHEMA, SESSION_USER, USER, VERSION | Not Supported | Supported |
| MySQL Encryption and Compression Functions | AES_DECRYPT, AES_ENCRYPT, COMPRESS, RANDOM_BYTES, SHA, SHA1, SHA2, STATEMENT_DIGEST, STATEMENT_DIGEST_TEXT, UNCOMPRESS, UNCOMPRESSED_LENGTH | Not Supported | Supported |
| Other Functions               | BITAND/BIT_AND, BITOR/BIT_OR, BITXOR/BIT_XOR, COALESCE, ISNULL, GREATEST, LAST_INSERT_ID, LEAST, SOUND╦EX, MD5 | Supported | Supported |

#### PL

YashanDB's yashan mode is compatible with most of the PL functionalities of MySQL databases, but there are some differences in syntax format, including:

- Data Types
- Control Flow
- Static SQL
- Dynamic SQL
- Exception Handling
  - System-defined Exceptions
  - User-defined Exceptions
- Cursors
- Stored Procedures
- Triggers
  - Row-level triggers are supported, but statement-level triggers are not supported.
  - Currently, only triggers on tables can be created; triggers on views are not supported.
- User UDF (UDF for SQL)
- JOB

#### Character Sets

YashanDB's yashan mode currently supports the following character sets:

|Character Set |YashanDB |MySQL |
|:---------| :------- | :----- |
| ASCII              | Supported | Supported |
| GBK                | Supported | Supported |
| UTF-8              | Supported | Supported |
| ISO88591           | Supported | Not Supported |
| GB18030            | Supported | Supported |

It also supports the following character set collation modes:

- ASCII_GENERAL_CS
- ASCII_GENERAL_CI
- GBK_GENERAL_CS
- GBK_GENERAL_CI
- UTF8_GENERAL_CS
- UTF8_GENERAL_CI
- UTF8_PINYIN_CS
- UTF8_PINYIN_CI
- ISO88591_GENERAL_CS
- ISO88591_GENERAL_CI
- GB18030_GENERAL_CS
- GB18030_GENERAL_CI
- GB18030_PINYIN_CS
- GB18030_PINYIN_CI

#### SQL Engine

In YashanDB's yashan mode, the SQL engine is compatible with most of the features of MySQL databases, including:

- Supports query rewriting.
- Supports precompiled statements.
- Supports cost-based optimizer.
- Supports plan generation and display (EXPLAIN).
- Supports execution plan buffer.
- Supports fast parameterization of execution plans.
- Supports Optimizer Hint.
- Supports fuzzy matching capabilities of like and reglike. 

#### Reserved Word Compatibility

YashanDB's yashan mode is compatible with most reserved words of MySQL databases, but currently, there are some reserved words that have certain differences, including:

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
