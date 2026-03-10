**dblink::=**

```ebnf+diagram
syntax::= "@" dblink_name
```

This syntax is used for table operations on remote databases (destination) by YashanDB (source), where the destination can be either a homogeneous database (YashanDB) or a heterogeneous database (Oracle).  

dblink_name refers to the remote database name created during [CREATE DATABASE LINK](../SQL Statements (yashan Mode)/CREATE DATABASE LINK).  

## Usage Instructions  

When using dblink functionality, YashanDB will initiate a [sandbox process](../../../Database Administration/Session and Plan Management/yex_server Sandbox Process Management) for related data operations.  

- When operating on remote database tables through dblink, the following requirements must be met:  
  >**Note**:
  >
  > In ISC Distributed Cluster Deployment, when operating on remote tables, column storage is used. In addition to the data type requirements listed in the table below, you must **follow the supported range of [data types](../Data Types (yashan Mode)/00Data Types (yashan Mode)) for column storage**.  

  |Destination is Oracle |Destination is YashanDB |
  |--------------------|--------------------------------------|
  | Must first [install the libaio library and download the Oracle Instant Client package](../../../Database Administration/Basic Database Management/Heterogeneous Database Link Configuration).  |-  |  
  | Remote table fields must be of the following data types:<br/>SMALLINT<br/>INT<br/>FLOAT/BINARY_FLOAT<br/>BINARY_DOUBLE<br/>NUMBER/DECIMAL<br/>DATE<br/>TIMESTAMP<br/>INTERVAL YEAR TO MONTH<br/>INTERVAL DAY TO SECOND<br/>CHAR<br/>VARCHAR<br/>NCHAR<br/>VARCHAR2<br/>NVARCHAR2<br/>RAW<br/>BLOB (not applicable when the source is ISC Distributed Cluster Deployment)<br/>CLOB (not applicable when the source is ISC Distributed Cluster Deployment)<br/>NCLOB (not applicable when the source is ISC Distributed Cluster Deployment)  | Remote table fields must be of the following data types:<br/>TINYINT<br/>SMALLINT<br/>INT<br/>BIGINT<br/>FLOAT/BINARY_FLOAT<br/>DOUBLE/BINARY_DOUBLE<br/>NUMBER<br/>BIT<br/>BOOLEAN<br/>DATE<br/>TIME<br/>TIMESTAMP<br/>INTERVAL YEAR TO MONTH<br/>INTERVAL DAY TO SECOND<br/>CHAR<br/>VARCHAR<br/>RAW<br/>ROWID (not applicable when the source is ISC Distributed Cluster Deployment)  |  

- In ISC Distributed Cluster Deployment, operations such as INSERT, UPDATE, DELETE, SEQUENCE, PROCEDURE, and FUNCTION on remote database tables are not permitted.  

- In scenarios using dblink, it does not support guaranteeing that all resources commit or roll back a transaction simultaneously through [Two-phase Commit](../../../Product Concepts/Appx Glossary.html#2para).  

- When performing transaction operations on a dblink remote database, only the READ COMMITTED transaction isolation level is supported.  

- Temporary tables are not currently supported; operations on remote temporary tables may produce unexpected results.  

## Configuring Sandbox Process Parameters  

Parameters related to dblink can be set in the $YASDB_DATA/external/server/yex_server.ini file. After YashanDB installation, the yex_server.ini file will not be automatically generated; you can create the file based on actual needs and adjust related parameter values.  

### CHARACTER\_SET

Specifies the character set used by the yex_server process, defaulting to UTF8. When recreating the database, if another character set is used, the contents of yex_server.ini will not synchronize and must be manually modified to keep the CHARACTER_SET parameter value consistent with the database character set.  

### DBLINK\_ROWARRAY\_SIZE

Maximum number of rows to fetch a batch of remote data, adjusted dynamically based on data size during execution. The default value is 32K, and the acceptable range is [1K,1T].  

If this value is adjusted to be less than the minimum value of 1K, no error will be reported, but the minimum value will still take effect.  

### DRV\_MEMORY\_BLOCK\_SIZE

Memory page size; YDBC_BUFFER will automatically split based on this parameter value. The default value is 512K, and the acceptable range is [64K,1T].  

If this value is adjusted to be less than the minimum value of 64K, no error will be reported, but the minimum value will still take effect.  

### MAX\_BEATING\_FAILS

Threshold for detecting heartbeat failures of the yex_server process; once the accumulated failure count exceeds this value, it is considered that the communication between yasdb and yex_server is interrupted, and the yex_server process will be restarted. The default value is 3, and the acceptable range is [3,255].  

If this value is adjusted to be less than the minimum value of 3, no error will be reported, but the minimum value will still take effect.  

### MAX\_DBLINK\_CONNS

In concurrent scenarios, when different user sessions access the same dblink, this defines the maximum number of connections used in the same time window. The default value is 64, and the acceptable range is [64,16384].  

If this value is adjusted to be less than the minimum value of 64, no error will be reported, but the minimum value will still take effect.  

## Querying Remote Tables  

When performing [SELECT](../SQL Statements (yashan Mode)/SELECT) operations on remote tables, the following constraints exist:  

- The table.column@dblink format cannot be used.  
- A maximum of 32 remote tables can be queried at once.  

***Example***  

```sql
-- Create remote table  
conn sys/********
CREATE TABLE table_test(c1 INT);

-- Create dblink and access the remote table  
conn sales/sales
CREATE DATABASE link link_test CONNECT TO sys IDENTIFIED BY sys USING '192.168.1.2:1688';
SELECT * FROM table_test@link_test;

-- Modify remote table metadata  
conn sys/********
ALTER TABLE table_test ADD COLUMN(c2 INT);

conn sales/sales

SELECT * FROM table_test@link_test;
C1           C2          
------------ ------------

```

## Inserting into Remote Tables  

When performing [INSERT](../SQL Statements (yashan Mode)/INSERT) operations on remote tables, the following constraints exist:  

- Multi-table INSERT is not allowed.  
- Specifying partition INSERT is not allowed.  
- INSERT DUPLICATE UPDATE statements are not allowed.  
- INSERT RETURN statements are not allowed.  
- INSERT SELECT does not support LOB types.  

***Example*** for Standalone Deployment and YAC Deployment  

```sql
-- Create remote table  
conn sys/********
CREATE TABLE table_test(c1 INT, c2 INT);

-- Create dblink and access the remote table  
conn sales/sales
CREATE DATABASE link link_test CONNECT TO sys IDENTIFIED BY sys USING '192.168.1.2:1688';
SELECT * FROM table_test@link_test;

-- Insert data  
INSERT INTO table_test@link_test VALUES(1,2);
```

## Updating Remote Tables  

When performing [UPDATE](../SQL Statements (yashan Mode)/UPDATE) operations on remote tables, the following constraints exist:  

- Filters for updating local database objects compared to the filter:  
  - Aggregate functions cannot be used.  
  - Window functions cannot be used.  
  - Subqueries cannot be used.  
  - Sequences cannot be used.  
  - UDFs (including UPDATE SET statements) cannot be used.  
- Multi-table UPDATE is not allowed.  
- Specifying partition UPDATE is not allowed.  

***Example*** for Standalone Deployment and YAC Deployment  

```sql
-- Create remote table  
conn sys/********
CREATE TABLE table_test(c1 INT, c2 INT);

-- Create dblink  
conn sales/sales
CREATE DATABASE link link_test CONNECT TO sys IDENTIFIED BY sys USING '192.168.1.2:1688';

-- Update remote table metadata  
UPDATE table_test@link_test SET c1=1,c2=2;
```

## Deleting from Remote Tables  

When performing [DELETE](../SQL Statements (yashan Mode)/DELETE) operations on remote tables, the following constraints exist:  

- Filters for deleting local database objects compared to the filter:  
  - Aggregate functions cannot be used.  
  - Window functions cannot be used.  
  - Subqueries cannot be used.  
  - Sequences cannot be used.  
  - UDFs cannot be used.  
- Multi-table DELETE is not allowed.  
- Specifying partition DELETE is not allowed.  

***Example*** for Standalone Deployment and YAC Deployment  

```sql
conn sys/********
CREATE TABLE table_test(c1 INT);

-- Create dblink  
conn sales/sales
CREATE DATABASE link link_test CONNECT TO sys IDENTIFIED BY sys USING '192.168.1.2:1688';

-- Delete remote table data  
DELETE FROM table_test@link_test WHERE c1=1;
```

## Calling Remote Procedures  

**callDblinkProc::=**

```ebnf+diagram
syntax::= func_name@dblink_name(arg1, arg2, ...)
```

When calling remote procedures, the following constraints exist:  

- The remote database must be an Oracle database.  

- The remote procedure objects can only be stored procedures, functions, or packages.  

- The parameter types of the remote procedures must be scalar data types.  

- If the remote procedure is an overloaded procedure, the following additional requirements apply:  

    - The number of parameters cannot be the same.  

    - When calling, all actual parameter values for all parameters in the target procedure must be provided.  

***Example*** for Standalone Deployment and YAC Deployment  

```sql
-- Create procedure in Oracle database  

conn oradb/oradb
CREATE FUNCTION func_test(c INT) RETURN INT IS
BEGIN
    RETURN 100;
END;
/

CREATE PROCEDURE proc_test(c INT) IS
BEGIN
    dbms_output.put_line(c);
END;
/

CREATE OR replace PACKAGE pkg_test AS
    FUNCTION  func_test(c NUMBER) RETURN INT;
    procedure proc_test(c number);
end pkg_test;
/
CREATE OR replace PACKAGE body pkg_test AS
    FUNCTION func_test(c NUMBER) RETURN INT IS
    BEGIN
        RETURN c + 100;
    END;
    PROCEDURE proc_test(c NUMBER) IS
    BEGIN
       dbms_output.put_line(c);
    END;
END pkg_test;
/

-- Create dblink  
conn sales/sales
CREATE DATABASE link link_test CONNECT TO oradb IDENTIFIED BY oradb USING 'oracle:192.168.1.3:1521/orainst';

-- Call remote procedures  
SELECT func_test@link_test(10) FROM dual;
SELECT pkg_test.func_test@link_test(10) FROM dual;
BEGIN
   proc_test@link_test(100);
   pkg_test.proc_test@link_test(100);
END;
/
```
