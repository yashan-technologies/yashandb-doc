
**dblink::=**

```ebnf
= "@" dblink_name.
```

This syntax is used for table operations on remote databases (destination) by YashanDB (source), where the destination can be either a homogeneous database (YashanDB) or a heterogeneous database (Oracle).  

dblink_name refers to the remote database name created during [CREATE DATABASE LINK](../../SQL Statements/CREATE DATABASE LINK).  

## Usage Instructions  

When using dblink functionality, YashanDB will initiate a [sandbox process](./yex_server Sandbox Process Management) for related data operations.  

- When operating on remote database tables through dblink, the following requirements must be met:  
  >**Note**:
  >
  > In ISC Distributed Cluster Deployment, when operating on remote tables, column storage is used. In addition to the data type requirements listed in the table below, you must **follow the supported range of [data types](../../Data Types/00Data Types) for column storage**.  

  |Destination is Oracle |Destination is YashanDB |
  |--------------------|--------------------------------------|
  | Remote table fields must be of the following data types:<br/>SMALLINT<br/>INT<br/>FLOAT/BINARY_FLOAT<br/>BINARY_DOUBLE<br/>NUMBER/DECIMAL<br/>DATE<br/>TIMESTAMP<br/>INTERVAL YEAR TO MONTH<br/>INTERVAL DAY TO SECOND<br/>CHAR<br/>VARCHAR<br/>NCHAR<br/>VARCHAR2<br/>NVARCHAR2<br/>RAW<br/>BLOB (not applicable when the source is ISC Distributed Cluster Deployment)<br/>CLOB (not applicable when the source is ISC Distributed Cluster Deployment)<br/>NCLOB (not applicable when the source is ISC Distributed Cluster Deployment)  | Remote table fields must be of the following data types:<br/>TINYINT<br/>SMALLINT<br/>INT<br/>BIGINT<br/>FLOAT/BINARY_FLOAT<br/>DOUBLE/BINARY_DOUBLE<br/>NUMBER<br/>BIT<br/>BOOLEAN<br/>DATE<br/>TIME<br/>TIMESTAMP<br/>INTERVAL YEAR TO MONTH<br/>INTERVAL DAY TO SECOND<br/>CHAR<br/>VARCHAR<br/>RAW<br/>ROWID (not applicable when the source is ISC Distributed Cluster Deployment)  |  

- In ISC Distributed Cluster Deployment, operations such as INSERT, UPDATE, DELETE, SEQUENCE, PROCEDURE, and FUNCTION on remote database tables are not permitted.  

- In scenarios using dblink, it does not support guaranteeing that all resources commit or roll back a transaction simultaneously through [Two-phase Commit](../../../../Product Concepts/Appx Glossary.md#2para).  

- When performing transaction operations on a dblink remote database, only the READ COMMITTED transaction isolation level is supported.  

- Temporary tables are not currently supported; operations on remote temporary tables may produce unexpected results.  

## Configuring Sandbox Process Parameters  

### DBLINK_ROWARRAY_SIZE

Maximum number of rows to fetch a batch of remote data, adjusted dynamically based on data size during execution. The default value is 32K, and the numeric part of the parameter value must be a positive integer or 0 with a valid range of [1K,1T].  

If this value is adjusted to be less than the minimum value of 1K, no error will be reported, but the minimum value will still take effect.  

### DRV_MEMORY_BLOCK_SIZE

Memory page size; YDBC_BUFFER will automatically split based on this parameter value. The default value is 512K, and the numeric part of the parameter value must be a positive integer or 0 with a valid range of [64K,1T].

If this value is adjusted to be less than the minimum value of 64K, no error will be reported, but the minimum value will still take effect.  

### EXS_MAX_XACTS

The maximum number of DBLink groups held simultaneously, a session can have at most one group. The default value is 1024, and the value must be a positive integer or 0 with a valid range of [1024,16384].

If this value is adjusted to an integer less than the minimum value of 1024, no error will be reported, but the actual effect still uses the minimum value.

### MAX_BEATING_FAILS

Threshold for detecting heartbeat failures of the yex_server process; once the accumulated failure count exceeds this value, it is considered that the communication between yasdb and yex_server is interrupted, and the yex_server process will be restarted. The default value is 3, and the value must be a positive integer or 0 with a valid range of [3,255].  

If this value is adjusted to an integer less than the minimum value of 1024, no error will be reported, but the actual effect still uses the minimum value.

### MAX_DBLINK_CONNS

In concurrent scenarios, when different user sessions access the same DBLink, this defines the maximum number of connections used in the same time window. The default value is 64, and the value must be a positive integer or 0 with a valid range of [64,16384].

If this value is adjusted to an integer less than the minimum value of 1024, no error will be reported, but the actual effect still uses the minimum value.

## Querying Remote Tables  

When performing [SELECT](../../SQL Statements/SELECT) operations on remote tables, the following constraints exist:  

- The table.column@dblink format cannot be used.  
- By default, 32 remote tables can be queried at once, and you can change the specification by modifing the system parameter `dblink_cursor_count`. 
- Cannot query hidden columns from remote tables.

***Example***  

```sql
-- Create remote table  
conn sys/********
create table table_test(c1 int);

-- Create dblink and access the remote table  
conn sales/sales
create database link link_test connect to sys identified by sys using '192.168.1.2:1688';
select * from table_test@link_test;

-- Modify remote table metadata  
conn sys/********
alter table table_test add column(c2 INT);

conn sales/sales

select * from table_test@link_test;
C1           C2          
------------ ------------

0 rows fetched.
```

## Inserting into Remote Tables  

When performing [INSERT](../../SQL Statements/INSERT) operations on remote tables, the following constraints exist:  

- Multi-table INSERT is not allowed.  
- Specifying partition INSERT is not allowed.  
- INSERT DUPLICATE UPDATE statements are not allowed.  
- INSERT RETURN statements are not allowed.  
- INSERT SELECT does not support LOB types.  

***Example*** for Standalone/YAC/Distributed Cluster Deployment  

```sql
-- Create remote table  
conn sys/********
create table table_test(c1 int, c2 int);

-- Create dblink and access the remote table  
conn sales/sales
create database link link_test connect to sys identified by sys using '192.168.1.2:1688';
select * from table_test@link_test;

-- Insert data  
insert into table_test@link_test values(1,2);
```

## Updating Remote Tables  

When performing [UPDATE](../../SQL Statements/UPDATE) operations on remote tables, the following constraints exist:  

- Filters for updating local database objects compared to the filter:  
  - Aggregate functions cannot be used.  
  - Window functions cannot be used.  
  - Subqueries cannot be used.  
  - Sequences cannot be used.  
  - UDFs (including UPDATE SET statements) cannot be used.  
- Multi-table UPDATE is not allowed.  
- Specifying partition UPDATE is not allowed.  

***Example*** for Standalone/YAC/Distributed Cluster Deployment  

```sql
-- Create remote table  
conn sys/********
create table table_test(c1 int, c2 int);

-- Create dblink  
conn sales/sales
create database link link_test connect to sys identified by sys using '192.168.1.2:1688';

-- Update remote table metadata  
update table_test@link_test set c1=1,c2=2;
```

## Deleting from Remote Tables  

When performing [DELETE](../../SQL Statements/DELETE) operations on remote tables, the following constraints exist:  

- Filters for deleting local database objects compared to the filter:  
  - Aggregate functions cannot be used.  
  - Window functions cannot be used.  
  - Subqueries cannot be used.  
  - Sequences cannot be used.  
  - UDFs cannot be used.  
- Multi-table DELETE is not allowed.  
- Specifying partition DELETE is not allowed.  

***Example*** for Standalone/YAC/Distributed Cluster Deployment  

```sql
conn sys/********
create table table_test(c1 int);

-- Create dblink  
conn sales/sales
create database link link_test connect to sys identified by sys using '192.168.1.2:1688';

-- Delete remote table data  
delete from table_test@link_test where c1=1;
```

## Calling Remote Procedures  

**callDblinkProc::=**

```ebnf
= func_name@dblink_name(arg1, arg2, ...).
```

When calling remote procedures, the following constraints exist:  

- The remote database must be an Oracle database.  

- The remote procedure objects can only be stored procedures, functions, or packages.  

- The parameter types of the remote procedures must be scalar data types.  

- If the remote procedure is an overloaded procedure, the following additional requirements apply:  

    - The number of parameters cannot be the same.  

    - When calling, all actual parameter values for all parameters in the target procedure must be provided.  

***Example*** for Standalone/YAC/Distributed Cluster Deployment  

```sql
-- Create procedure in Oracle database  

conn oradb/oradb
create function func_test(c int) return int is
begin
    return 100;
end;
/

create procedure proc_test(c int) is
begin
    dbms_output.put_line(c);
end;
/

create or replace package pkg_test as
    function  func_test(c number) return int;
    procedure proc_test(c number);
end pkg_test;
/
create or replace package body pkg_test as
    function func_test(c number) return int is
    begin
        return c + 100;
    end;
    procedure proc_test(c number) is
    begin
       dbms_output.put_line(c);
    end;
end pkg_test;
/

-- Create dblink  
conn sales/sales
create database link link_test connect to oradb identified by oradb using 'oracle:192.168.1.3:1521/orainst';

-- Call remote procedures  
select func_test@link_test(10) from dual;
select pkg_test.func_test@link_test(10) from dual;
begin
   proc_test@link_test(100);
   pkg_test.proc_test@link_test(100);
end;
/
```

