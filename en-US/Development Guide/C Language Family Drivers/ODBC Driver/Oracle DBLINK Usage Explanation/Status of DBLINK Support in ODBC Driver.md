This chapter will list the support status of the ODBC driver for Oracle DBLINK.

## Supported Functionality

### Sending Commands via Oracle Advanced Package

The Oracle database can use the DBMS_HS_PASSTHROUGH advanced package to send commands to the YashanDB database. This advanced package supports passing `ALTER`, `CREATE`, `DROP`, and `GRANT` statements. For specific descriptions and limitations of the advanced package, please refer to the Oracle official documentation.

This advanced package does not support passing the following commands:

- `BEGIN TRANSACTION`
- `COMMIT`
- `ROLLBACK`
- `SAVE`
- `SHUTDOWN`

***Example***

```plsql
DECLARE
  num_rows INTEGER;
BEGIN
  num_rows := DBMS_HS_PASSTHROUGH.EXECUTE_IMMEDIATE@YASDBODBC('insert into t1 values (1),(2)');
END;
/
--Success

DECLARE
  num_rows INTEGER;
BEGIN
  num_rows := DBMS_HS_PASSTHROUGH.EXECUTE_IMMEDIATE@YASDBODBC('commit');
END;
/
--The COMMIT command is not supported; returns an error
```

### COPY Command

This command can be used to copy table data between different databases. This feature is an SQLPlus feature and is supported only in SQLPlus.

Syntax:

```ebnf
= COPY {FROM database | TO database | FROM database TO database}{APPEND|CREATE|INSERT|REPLACE} destination_table [(column, column, column, ...)] USING query.
```

**database::=**

```ebnf
= username[/password]@connect_identifier.
```

Supported data types:

- CHAR
- DATE
- LONG
- NUMBER
- VARCHAR2

Supports four methods: APPEND, CREATE, INSERT, and REPLACE. For detailed descriptions of these methods, please refer to the SQLPlus official documentation. If the executed statement causes a new table to be created in the YashanDB database, it will return an error.

***Example***

```sql
--In this example, oracle@ORCL is the Oracle database connection for the oracle user, and YASDBODBC is the configured YashanDB DBLINK
--Preliminary table creation
YashanDB:
create table copy_table (col1 int);
Oracle:
create table copy_table (col1 int);

--INSERT
copy from oracle@ORCL insert copy_table@YASDBODBC using select * from copy_table;
copy to oracle@ORCL insert copy_table using select * from copy_table@YASDBODBC;

--CREATE
copy to oracle@ORCL create copy_tablenew using select * from copy_table@YASDBODBC;
--Since the table copy_tablenew does not exist in YashanDB, executing the following statement will create this table in the YashanDB database and return an error
copy from oracle@ORCL create copy_tablenew@YASDBODBC using select * from copy_table;
ORA-02021: DDL operations are not allowed on a remote database

--APPEND
copy from oracle@ORCL append copy_table@YASDBODBC using select * from copy_table;
copy to oracle@ORCL append copy_table using select * from copy_table@YASDBODBC;

--REPLACE
copy to oracle@ORCL replace copy_table using select * from copy_table@YASDBODBC;
--Since the table copy_table already exists in YashanDB, the following statement will delete this table and create a new table containing the copied data, returning an error
copy from oracle@ORCL replace copy_table@YASDBODBC using select * from copy_table;
ORA-02021: DDL operations are not allowed on a remote database
```

### CRUD Capabilities

Supports executing `DELETE`, `INSERT`, `SELECT`, and `UPDATE` statements by specifying DBLINK to connect remotely to the YashanDB database. For detailed descriptions and limitations of the statements, please refer to the Oracle official documentation.

***Example***

```sql
--Table creation statement
YashanDB: 
create table example_table (col1 int);
INSERT INTO example_table VALUES (4);
INSERT INTO expmple_table VALUES (6);

--SELECT
SELECT * FROM example_table@YASDBODBC;

--INSERT (multiple values sets are not supported)
INSERT INTO example_table@YASDBODBC VALUES (2);

--UPDATE
UPDATE example_table@YASDBODBC SET col1=8 WHERE col1=6;

--DELETE
DELETE FROM example_table@YASDBODBC;
```

### Parameter Binding Support

Supports parameter binding for input and output of ODBC.

## Unsupported Functionality

- Data Dictionary