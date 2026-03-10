YashanDB (yashan mode) is compatible with the basic functionalities of SQL syntax, expression operations, FILTER CONDITION, data types, built-in functions, system views, and PL with Oracle database. Database management and development personnel do not need to spend a lot of time learning new knowledge; they can directly refer to the relevant Oracle documentation for delivered features and operate smoothly with YashanDB, achieving a smooth migration from Oracle database to YashanDB.

In certain other functionalities, YashanDB may differ in performance or may not currently be compatible with Oracle database for the following reasons:

- YashanDB has a different underlying architecture and product form than Oracle database.
- YashanDB has eliminated some outdated methods that do not conform to current mainstream business frameworks and has added its own features.

This document will specifically illustrate the compatibility of YashanDB with Oracle database in the following aspects under Standalone Deployment and columnar storage mode:

- SQL syntax
- Expression operations
- FILTER CONDITION
- Data types
- Built-in functions
- PL
- System views
- Character set
- SQL engine
- Database security
- Tool compatibility
- Other compatibility

## SQL Syntax

YashanDB supports mainstream SQL syntax from Oracle database. Other minor incompatibilities due to functionality deficiencies will report a syntax unsupported error. At this time, please contact our technical support for alternative solutions. For detailed SQL syntax description, please refer to [SQL statement](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/00SQL Statements (yashan Mode)).

**(1) DML Class**

**SELECT**

- Supports most query functionalities, including single/multiple table queries, subqueries, inner joins, semi-joins, outer joins, grouping and aggregation, hierarchical queries, etc.
- Supports set operations such as UNION, UNION ALL, INTERSECT, MINUS, etc.
- Supports viewing execution plans in the following ways:
  - EXPLAIN
  - AUTOTRACE
- Supports random sampling query capabilities.
- CTE supports recursive functionality.

**INSERT**

- Supports single-row and multi-row insertions and specifies partition insertions.
- Supports INSERT INTO SELECT statements.
- Supports INSERT ALL statements.
- Supports single-table and multi-table inserts.

**UPDATE**

- Supports updating single and multiple columns.
- Supports using subqueries.

**DELETE**

- Supports single and multiple table deletions.
- Supports using subqueries.

**(2) DDL Class**

YashanDB is compatible with most object and object management operations of Oracle database, including:

- HEAP Tables:
  - CREATE TABLE/CREATE TABLE AS
  - ALTER TABLE
  - DROP TABLE
  - TRUNCATE TABLE
- Temporary Tables:
  - GLOBAL TEMPORARY TABLE
  - PRIVATE TEMPORARY TABLE
- Partition Tables:
  - Supports RANGE/INTERVAL/LIST/HASH types of partitions.
  - Supports ADD|DROP|TRUNCATE|SPLIT|MERGE PARTITION operations.
  - Supports partition row migration.
  - Supports secondary partitions.
- External Tables:
  - Supports creating directories, CREATE DIRECTORY.
  - Supports creating and deleting external tables.
- Constraints:
  - Includes in_line constraints/out_of_line constraints.
  - Supports UNIQUE, PRIMARY KEY, FOREIGN KEY, CHECK, (NOT) NULL type constraints.
- Views:
  - CREATE VIEW/CREATE OR REPLACE FORCE VIEW
  - DROP VIEW
  - Views support SELECT/INSERT/UPDATE/DELETE.
- Materialized Views:
  - CREATE MATERIALIZED VIEW
  - ALTER MATERIALIZED VIEW
  - DROP MATERIALIZED VIEW
  - Supports local materialized views; remote materialized views are not supported.
- BTree Indexes:
  - Includes global indexes and local indexes.
  - Includes unique indexes and non-unique indexes.
  - Includes reverse key indexes and function indexes.
  - Supports REBUILD|UNUSABLE|COALESCE|PARALLEL|RENAME on indexes.
  - Supports creating multiple indexes on the same column.
- Synonyms:
  - Includes private synonyms and public synonyms.
- Sequences:
  - Includes ascending sequences and descending sequences; can specify CYCLE|NOCYCLE, CACHE|NOCACHE, ORDER|NOORDER.
  - Sequences support NEXTVAL and CURRVAL.
- DBLINK
  - Supports remote connections from Oracle to YashanDB, YashanDB to Oracle, and YashanDB to YashanDB.
  - Supports PUBLIC and PRIVATE mode links.
  - Supports connections for tables and views.
  - Supports creating synonyms for remote objects in YashanDB, calling remote stored procedures on YashanDB, and viewing LOB data.

**(3)HINT**

YashanDB supports the use of HINTs and adopts Oracle's HINT syntax, enabling specification of join methods, join orders, table scans, index scans, etc. For detailed information on HINTs, please refer to [hint](../../Development Guide/SQL Reference Manual/General SQL Syntax/hint).

## Expression Operations

YashanDB includes mainstream computational frameworks that implement expression operations. Additionally, Oracle itself lacks a Boolean type (which is only supported in its PL), requiring the use of other data types for operations; YashanDB implements direct Boolean expression operations. Details are shown in the table below:

|Expression Operation Type |YashanDB |Oracle Database |
| -------------- | -------- | ------------ |
| Binary Addition           | Supported| Supported       |
| Binary Subtraction        | Supported| Supported       |
| Binary Multiplication     | Supported| Supported       |
| Binary Division           | Supported| Supported       |
| Binary Modulus            | Supported| Supported       |
| Unary Negation            | Supported| Supported       |
| Bitwise AND              | Supported| Supported       |
| Bitwise OR               | Supported| Supported       |
| Bitwise XOR              | Supported| Supported       |
| Boolean Operations        | Supported| Not Supported   |

## FILTER CONDITION

YashanDB's FILTER CONDITION type is fully compatible with Oracle database's FILTER CONDITION type. Details are shown in the table below:

|FILTER CONDITION |YashanDB |Oracle Database |
| --------------- | -------- | ------------ |
| ALL              | Supported| Supported       |
| ANY              | Supported| Supported       |
| AND              | Supported| Supported       |
| EQUAL            | Supported| Supported       |
| NOT EQUAL        | Supported| Supported       |
| EXISTS           | Supported| Supported       |
| NOT EXISTS       | Supported| Supported       |
| GREAT EQUAL      | Supported| Supported       |
| GREATER          | Supported| Supported       |
| IN               | Supported| Supported       |
| NOT IN           | Supported| Supported       |
| IS NULL          | Supported| Supported       |
| IS NOT NULL      | Supported| Supported       |
| LESS             | Supported| Supported       |
| LESS EQUAL       | Supported| Supported       |
| LIKE             | Supported| Supported       |
| NOT LIKE         | Supported| Supported       |
| REG LIKE         | Supported| Supported       |
| NOT REG LIKE     | Supported| Supported       |
| OR               | Supported| Supported       |
| SOME             | Supported| Supported       |

## Data Types

YashanDB currently includes 27 data types. The comparison with Oracle database is shown in the table below:

|Data Type |YashanDB |Oracle Database |
| ------------------------------ | -------- | ------------ |
| BOOLEAN                        | Supported| Not Supported   |
| TINYINT                        | Supported| Not Supported   |
| SMALLINT                       | Supported| Supported       |
| INTEGER                        | Supported| Supported       |
| BIGINT                         | Supported| Not Supported   |
| FLOAT                          | Supported| Supported       |
| DOUBLE                         | Supported| Supported       |
| NUMBER                         | Supported| Supported       |
| DATE                           | Supported| Supported       |
| TIMESTAMP                      | Supported| Supported       |
| TIMESTAMP WITH LOCAL TIME ZONE | Supported| Supported       |
| TIMESTAMP WITH TIME ZONE       | Supported| Supported       |
| TIME                           | Supported| Not Supported   |
| INTERVAL YEAR TO MONTH         | Supported| Supported       |
| INTERVAL DAY TO SECOND         | Supported| Supported       |
| CHAR                           | Supported| Supported       |
| VARCHAR                        | Supported| Supported       |
| NCHAR                          | Supported| Supported       |
| NVARCHAR                       | Supported| Supported       |
| ST_GEOMETRY                    | Supported| Supported       |
| RAW                            | Supported| Supported       |
| CLOB                           | Supported| Supported       |
| NCLOB                          | Supported| Supported       |
| BLOB                           | Supported| Supported       |
| BIT                            | Supported| Not Supported   |
| ROWID                          | Supported| Supported       |
| UROWID                         | Supported| Supported       |
| CURSOR                         | Supported| Supported       |
| JSON                           | Supported| Supported       |
| XMLTYPE                        | Supported| Supported       |

> **Note**: 
>
> - YashanDB's large object (CLOB/BLOB) data types have strong storage capabilities, but their access performance is not recommended for use in complex scenarios.

## Built-in Functions

YashanDB currently implements over 120 built-in functions. For the specific list and description, please refer to [Built-in functions](../../Development Guide/SQL Reference Manual/Built-in Functions (yashan Mode)/00Built-in Functions (yashan Mode)).

The comparison with Oracle database's built-in functions is shown in the table below:

|Function Type |Supported Function List |YashanDB |Oracle Database |
| ------------ | ------------------------------------------------------------ | -------- | ------------ |
| Mathematical Functions| ABS, ACOS, ASIN, ATAN, ATAN2, COS, COT, CEIL, DIV, EXP, FLOOR, LOG, LN, MOD, PI, POW, POWER, ROUND, SIGN, SIN, SINH, STDDEV, STDDEV_POP, STDDEV_SAMP, SQRT, TAN, TANH, TRUNC, VARIANCE, VAR_POP, VAR_SAMP, … | Supported| Supported       |
| String Functions     | ASCII, CHR, CONCAT, INITCAP, INSTR, INSTRB, LEFT, LENGTH, LENGTH2, LOWER, LPAD, LTRIM, NLSSORT, POSITION, REPLACE, RIGHT, RPAD, RTRIM, SPLIT, STRPOS, SUBSTR, SUBSTRB, SOUNDEX, TRIM, TRANSLATE … USING, UNISTR, UPPER, empty_LOB, … | Supported| Supported       |
| Regex Functions       | REGEXP_LIKE, REGEXP_COUNT, REGEXP_INSTR, REGEXP_REPLACE, REGEXP_SUBSTR, … | Supported| Supported       |
| Conversion Functions   | BIN_TO_NUM, CAST, NUMTODSINTERVAL, NUMTOYMINTERVAL, ROWIDTOCHAR, TRANSLATE, TO_CHAR, TO_DATE, TO_DSINTERVAL, TO_NUMBER, TO_TIMESTAMP, TO_YMINTERVAL, … | Supported| Supported       |
| Collection Functions  | COALESCE, DECODE, GREATEST, LEAST, NVL, NVL2, …                      | Supported| Supported       |
| Aggregate Functions    | AVG, COUNT, GROUP_CONCAT, LISTAGG, MAX, MIN, SUM, MEDIAN, PERCENTILE_CONT, … | Supported| Supported       |
| Window Functions       | AVG, COUNT, FIRST, FIRST_VALUE, LAST, LAST_VALUE, LEAD, MAX, SUM, RANK, ROW_NUMBER, … | Supported| Supported       |
| System Functions       | SCN_TO_TIMESTAMP, TIMESTAMP_TO_SCN, USERENV, …                      | Supported| Supported       |
| Time Functions         | ADD_MONTHS, CURRENT_TIMESTAMP, EXTRACT, LAST_DAY, NOW, NEXT_DAY, OVERLAPS, … | Supported| Supported       |
| Conditional Functions   | IF, IFNULL, ISNULL, NULLIF                                       | Supported| Not Supported   |
| JSON Functions          | JSON, JSON_ARRAY_GET, JSON_ARRAY_LENGTH, JSON_EXISTS, JSON_FORMAT, JSON_PARSE, JSON_QUERY, JSON_SERIALIZE, JSON_VALUE, … | Supported| Supported       |
| Random Functions        | RANDOM                                                           | Supported| Not Supported   |
| Other Functions         | HEXTORAW, SQLCODE, SQLERRM, SYS_CONNECT_BY_PATH, …               | Supported| Supported       |

## PL

YashanDB is compatible with most PL functionalities of Oracle database, including:

- Data types
- Control flow
- Static SQL
- Dynamic SQL
- Exception handling
  - System-defined exceptions
  - User-defined exceptions
- Cursors
  - BULK COLLECT
- Stored Procedures
  - Procedure body encryption wrap functionality
- Triggers
  - Supports row-level and statement-level triggers.
  - Currently only supports creating triggers on tables, not on views.
- User-defined Procedures
  - Supports subprocedures
  - Supports global variables and TYPE definitions
  - PROCEDURE and FUNCTION support overloading
- User-defined Functions
  - SQL language UDF
  - External JAVA language UDF
  - External C language UDF
- User-defined Data Types
  - OBJECT TYPE
  - VARRAY TYPE
  - TABLE TYPE
  - Supports EXECUTE and UNDER ON object-level privilege control
- JOB

## Built-in Advanced Packages

YashanDB is compatible with some of Oracle database's built-in advanced packages. Details are shown in the table below:

|Advanced Package Name |
| --------------------- |
| DBMS_AUDIT_MGMT       |
| DBMS_APPLICATION_INFO |
| DBMS_CRYPTO           |
| DBMS_DESCRIBE         |
| DBMS_HM               |
| DBMS_IJOB             |
| DBMS_JOB              |
| DBMS_LOB              |
| DBMS_LOCK             |
| DBMS_METADATA         |
| DBMS_MVIEW            |
| DBMS_OUTPUT           |
| DBMS_RANDOM           |
| DBMS_RESOURCE_MANAGER |
| DBMS_ROWID            |
| DBMS_SCHEDULER        |
| DBMS_SESSION          |
| DBMS_SQL              |
| DBMS_STANDARD         |
| DBMS_STATS            |
| DBMS_UTILITY          |
| DBMS_XA               |
| OWA_UTIL              |
| UTL_ENCODE            |
| UTL_FILE              |
| UTL_I18N              |
| UTL_RAW               |

## System Views

YashanDB is compatible with the following DBA views from Oracle database (the compatibility list for ALL/USER views corresponds to the DBA views):

|View Name |
| ------------------------------ |
| AUDITABLE_SYSTEM_ACTIONS       |
| AUDIT_UNIFIED_ENABLED_POLICIES |
| AUDIT_UNIFIED_POLICIES         |
| COL                            |
| DICT_COLUMNS                   |
| DICTIONARY                     |
| DICT                           |
| DBA_ALL_TABLES                 |
| DBA_ARGUMENTS                  |
| DBA_AUDIT_MGMT_CLEANUP_JOBS    |
| DBA_AUDIT_MGMT_LAST_ARCH_TS    |
| DBA_COLL_TYPES                 |
| DBA_COL_COMMENTS               |
| DBA_CONSTRAINTS                |
| DBA_CONS_COLUMNS               |
| DBA_DATA_FILES                 |
| DBA_DB_LINKS                   |
| DBA_DEPENDENCIES               |
| DBA_EXTENTS                    |
| DBA_EXTERNAL_TABLES            |
| DBA_FREE_SPACE                 |
| DBA_HISTOGRAMS                 |
| DBA_INDEXES                    |
| DBA_IND_COLUMNS                |
| DBA_IND_EXPRESSIONS            |
| DBA_IND_PARTITIONS             |
| DBA_IND_STATISTICS             |
| DBA_IND_SUBPARTITIONS          |
| DBA_JOBS                       |
| DBA_LOBS                       |
| DBA_LOB_PARTITIONS             |
| DBA_LOB_SUBPARTITIONS          |
| DBA_LOG_GROUPS                 |
| DBA_MVIEWS                     |
| DBA_NESTED_TABLES              |
| DBA_OBJECTS                    |
| DBA_OUTLINES                   |
| DBA_OUTLINE_HINTS              |
| DBA_PART_COL_STATISTICS        |
| DBA_PART_HISTOGRAMS            |
| DBA_PART_INDEXES               |
| DBA_PART_KEY_COLUMNS           |
| DBA_PART_TABLES                |
| DBA_PROCEDURES                 |
| DBA_PROFILES                   |
| DBA_RECYCLEBIN                 |
| DBA_ROLES                      |
| DBA_ROLE_PRIVS                 |
| DBA_SCHEDULER_JOBS             |
| DBA_SEGMENTS                   |
| DBA_SEQUENCES                  |
| DBA_SOURCE                     |
| DBA_SUBPARTITION_TEMPLATES     |
| DBA_SUBPART_KEY_COLUMNS        |
| DBA_SYNONYMS                   |
| DBA_SYS_PRIVS                  |
| DBA_TABLES                     |
| DBA_TABLESPACES                |
| DBA_TAB_COLS                   |
| DBA_TAB_COLUMNS                |
| DBA_TAB_COL_STATISTICS         |
| DBA_TAB_COMMENTS               |
| DBA_TAB_HISTOGRAMS             |
| DBA_TAB_MODIFICATIONS          |
| DBA_TAB_PARTITIONS             |
| DBA_TAB_PRIVS                  |
| DBA_TAB_STATISTICS             |
| DBA_TAB_STAT_PREFS             |
| DBA_TAB_SUBPARTITIONS          |
| DBA_TEMP_FILES                 |
| DBA_TRIGGERS                   |
| DBA_TRIGGER_COLS               |
| DBA_TRIGGER_ORDERING           |
| DBA_TYPES                      |
| DBA_TYPE_ATTRS                 |
| DBA_TYPE_METHODS               |
| DBA_USERS                      |
| DBA_VIEWS                      |
| ROLE_SYS_PRIVS                 |
| ROLE_TAB_PRIVS                 |
| UNIFIED_AUDIT_TRAIL            |

YashanDB is compatible with the following dynamic views from Oracle database:

|View Name |
| ------------------------ |
| V$2PC_PENDING            |
| V$ARCHIVE_DEST           |
| V$ARCHIVE_DEST_STATUS    |
| V$ARCHIVE_GAP            |
| V$ARCHIVED_LOG           |
| V$BUFFER_POOL            |
| V$BUFFER_POOL_STATISTICS |
| V$CONTROLFILE            |
| V$DATABASE               |
| V$DATAFILE               |
| V$DIAG_INCIDENT          |
| V$DIAG_PROBLEM           |
| V$EVENT_HISTOGRAM        |
| V$EVENT_NAME             |
| V$FIXED_TABLE            |
| V$FIXED_VIEW_DEFINITION  |
| V$HM_CHECK               |
| V$HM_CHECK_PARAM         |
| V$HM_FINDING             |
| V$HM_RUN                 |
| V$INSTANCE               |
| V$LOCK                   |
| V$LOCKED_OBJECT          |
| V$LOGFILE                |
| V$MYSTAT                 |
| V$OPEN_CURSOR            |
| V$OSSTAT                 |
| V$PARAMETER              |
| V$PQ_TQSTAT              |
| V$PROCESS                |
| V$PX_SESSION             |
| V$RECOVERY_PROGRESS      |
| V$RESERVED_WORDS         |
| V$ROLLBACK               |
| V$SEGMENT_STATISTICS     |
| V$SEGSTAT                |
| V$SESS_TIME_MODEL        |
| V$SESSION                |
| V$SESSION_EVENT          |
| V$SESSION_WAIT           |
| V$SESSTAT                |
| V$SGA                    |
| V$SGASTAT                |
| V$SQL                    |
| V$SQL_BIND_CAPTURE       |
| V$SQL_PLAN               |
| V$SQL_PLAN_STATISTICS    |
| V$SQLAREA                |
| V$SQLSTATS               |
| V$SQLTEXT                |
| V$STATNAME               |
| V$SYSSTAT                |
| V$SYSTEM_EVENT           |
| V$SYSTEM_PARAMETER       |
| V$SYSTEM_WAIT_CLASS      |
| V$TABLESPACE             |
| V$TEMP_EXTENT_POOL       |
| V$TEMPSEG_USAGE          |
| V$TRANSACTION            |
| V$UNDOSTAT               |
| V$VERSION                |

## Character Set

YashanDB supports the following character sets:

- ASCII
- GBK
- UTF8
- ISO88591
- GB18030

At the same time, YashanDB supports the following character set sorting methods:

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

## SQL Engine

YashanDB's SQL engine is compatible with most features of Oracle database, including:

- Supports query rewriting.
- Supports precompiled statements.
- Supports cost-based optimizer.
- Supports execution plan generation and display (EXPLAIN).
- Supports execution plan buffer.
- Supports quick parameterization of execution plans.
- Supports execution plan binding.
- Supports Optimizer Hints.
- Supports OutLine.
- Supports fuzzy matching capabilities with like and reglike.

## Database Security

**Privilege Management**

- Compatible with the system-level privileges of Oracle database.
- Compatible with common object privileges, supports table and view object-level privilege management and usage.
- Supports granting and revoking privileges.
  - with admin option
  - with grant option
- Supports creating user-defined roles.
- Supports DBA and PUBLIC system predefined roles and their privilege authentication.
- Provides views to query privilege-related content.

**Authentication**

- Compatible with Oracle database's password policy.
- Profiles support user password management.

**Auditing**

- Compatible with Oracle database's unified auditing.

## Tool Compatibility

**imp**

- Supports FULL, FROM/TOUSER, and TABLES modes for dimensional import.
- Supports importing specified metadata and data.
- Supports overwriting existing tables, i.e., truncate mode.

**exp**

- Supports FULL, OWNER, and TABLES modes for dimensional export.
- Supports exporting specified metadata and data.

**Operational Tools**

- Supports 10053 trace events.

## Other Compatibility

**Statistics**

YashanDB's statistics system is compatible with most features of Oracle database, including:

- Supports collecting statistics for tables (partitioned tables), indexes, columns, etc.
- Supports dynamic sampling functionality.
- Supports locking statistics.
- Supports collecting column histogram statistics.
- Supports collecting statistics tasks configured through JOB.
- Supports importing and exporting statistics using advanced package DBMS_STATS.
- Supports real-time collection of basic statistics.

**Tablespaces**

- Supports ONLINE/OFFLINE.
- Supports RENAME.

**Reliability**

- Supports full database flashback, flashback DML operations, flashback query functionality.