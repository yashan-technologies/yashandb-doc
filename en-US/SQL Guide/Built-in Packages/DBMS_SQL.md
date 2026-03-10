The DBMS_SQL package provides a set of built-in interfaces for executing dynamic SQL, including DDL and DML.

## OPEN\_CURSOR

```plsql
DBMS_SQL.OPEN_CURSOR (
   security_level                 IN     INTEGER    DEFAULT 1,
   treat_as_client_for_results    IN     BOOLEAN    DEFAULT FALSE) 
  RETURN INTEGER;
```

OPEN_CURSOR is used to open a new cursor and returns the cursor ID value, which is of INTEGER type.

|Parameter |Description |
| :---- |:-----------|
| security_level | Security level, of INTEGER type. Default value is 1, other valid values are syntax compatible. |
| treat_as_client_for_results | Syntax compatible.           |

Description:

- The opened cursor is a session-level resource; it is released when actively closed or when the session disconnects.
- A cursor can execute a SQL statement repeatedly or parse a new SQL statement without needing to close and reopen the cursor.
- Only security level 1 is supported, where user verification is checked in the binding variable and execution phases to ensure it is the same as in the parsing phase.

***Example***

```plsql
DECLARE
  C INTEGER;
BEGIN
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

```

## CLOSE\_CURSOR

```plsql
DBMS_SQL.CLOSE_CURSOR (
   c    IN OUT INTEGER);
```

CLOSE_CURSOR is used to close a given cursor and release the corresponding cursor resources.

|Parameter |Description |
| :---- |:-----------|
| c | Cursor ID to be released, of INTEGER type.<br/> Upon successful execution, it can serve as an output parameter and will be automatically set to NULL. |

***Example***

```plsql
DECLARE
  C INTEGER;
BEGIN
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

```

## PARSE

```plsql
DBMS_SQL.PARSE (
   c                           IN   INTEGER,
   statement                   IN   VARCHAR2,
   language_flag               IN   INTEGER[
 [,edition                     IN   VARCHAR2 DEFAULT NULL],
   apply_crossedition_trigger  IN   VARCHAR2 DEFAULT NULL,
   fire_apply_trigger          IN   BOOLEAN DEFAULT TRUE]
 [,schema                      IN   VARCHAR2 DEFAULT NULL]
 [,container                   IN   VARCHAR2)];
```

or:

```plsql
DBMS_SQL.PARSE (
   c                           IN   INTEGER,
   statement                   IN   CLOB,
   language_flag               IN   INTEGER[
 [,edition                     IN   VARCHAR2 DEFAULT NULL],
   apply_crossedition_trigger  IN   VARCHAR2 DEFAULT NULL,
   fire_apply_trigger          IN   BOOLEAN DEFAULT TRUE]
 [,schema                      IN   VARCHAR2 DEFAULT NULL]
 [,container                   IN   VARCHAR2)];  
```

PARSE is used to parse the given SQL statement using the provided cursor.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| statement | The SQL statement to be parsed, of VARCHAR or CLOB type.<br/> If the length of the SQL statement exceeds 32K, CLOB type must be used. |
| language_flag | Valid values range from 0 to 6, commonly used value is constant DBMS_SQL.NATIVE. Syntax compatible. |
| edition | Syntax compatible.                                  |
| apply_crossedition_trigger | Syntax compatible.     |
| fire_apply_trigger | Syntax compatible.          |
| schema | Specifies the SCHEMA for parsing non-qualifying object names; if NULL, uses the current SCHEMA. |
| container | Syntax compatible.                                 |

Description:

- The maximum length of a SQL statement cannot exceed 2M.
- DDL statements are executed immediately during the PARSE process.

***Example***

```plsql
DECLARE
  C INTEGER;
  V_SQL VARCHAR(32000);
BEGIN
  V_SQL := 'SELECT * FROM dual';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

```

## BIND\_VARIABLE

```plsql
DBMS_SQL.BIND_VARIABLE (
   c              IN INTEGER,
   name           IN VARCHAR2,
   value          IN <datatype>);
```

or:

```plsql
DBMS_SQL.BIND_VARIABLE (
   c              IN INTEGER,
   name           IN VARCHAR2,
   value          IN VARCHAR2 [,out_value_size IN INTEGER]);
```

BIND_VARIABLE binds a value to a bind variable based on the variable name in the statement.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| name | The name of the bind parameter, of VARCHAR type. Needs to match the bind variable name in the statement. |
| value | The value to bind to the bind variable, of any type supported by the SQL statement. |
| out_value_size | Specifies the output size for the VARCHAR type, of INTEGER type. |

Description:

- The leading colon in the name parameter can be omitted.
- If the out_value_size is specified, it is treated as if the value is of VARCHAR type.

***Example***
```plsql
INSERT INTO department VALUES('111', 'R&D Dep');

DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
BEGIN
  V_SQL := 'SELECT * FROM department WHERE department_name = :B1';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.BIND_VARIABLE(C, ':B1', 'R&D Dep');
  R := DBMS_SQL.EXECUTE(C);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/


DELETE FROM department WHERE deparment_no='111';
```

## BIND\_VARIABLE\_RAW

```plsql
DBMS_SQL.BIND_VARIABLE_RAW (
   c              IN INTEGER,
   name           IN VARCHAR2,
   value          IN RAW [,out_value_size IN INTEGER]);
```

BIND_VARIABLE_RAW is used to bind a RAW type value to a bind variable.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| name | The name of the bind parameter, of VARCHAR type. Needs to match the bind variable name in the statement. |
| value | The value to bind to the bind variable, of RAW type. |
| out_value_size | Specifies the output size for RAW type, of INTEGER type. |

***Example***
```plsql
CREATE TABLE bind_raw(c1 INT, c2 RAW(100));
INSERT INTO bind_raw VALUES(1, '1');
COMMIT;

DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
BEGIN
  V_SQL := 'SELECT * FROM bind_raw WHERE C2 = :B1';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.BIND_VARIABLE_RAW(C, ':B1', '1');
  R := DBMS_SQL.EXECUTE(C);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/


DROP TABLE bind_raw;
```

## BIND\_VARIABLE\_CHAR

```plsql
DBMS_SQL.BIND_VARIABLE_CHAR (
   c              IN INTEGER,
   name           IN VARCHAR2,
   value          IN CHAR [,out_value_size IN INTEGER]);
```

BIND_VARIABLE_CHAR is used to bind a CHAR type value to a bind variable.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| name | The name of the bind parameter, of VARCHAR type. Needs to match the bind variable name in the statement. |
| value | The value to bind to the bind variable, of CHAR type. |
| out_value_size | Specifies the output size for RAW type, of INTEGER type. |

***Example***
```plsql
CREATE TABLE bind_char(c1 INT, c2 CHAR(100));
INSERT INTO bind_char VALUES(1, '1');
COMMIT;

DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
BEGIN
  V_SQL := 'SELECT * FROM bind_char WHERE C2 = :B1';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.BIND_VARIABLE_CHAR(C, ':B1', '1');
  R := DBMS_SQL.EXECUTE(C);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/


DROP TABLE bind_char;
```

## BIND\_ARRAY

```plsql
DBMS_SQL.BIND_ARRAY ( 
   c                   IN INTEGER, 
   name                IN VARCHAR2, 
   <table_variable>    IN <datatype> 
 [,index1              IN INTEGER, 
   index2              IN INTEGER)] ); 
```

BIND_ARRAY is used for bulk binding variables and executing bulk DML statements.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| name | The name of the bind parameter, of VARCHAR type. Needs to match the bind variable name in the statement. |
| table_variable | The values to bind to the bind variable, which should be of a public nested table type defined in DBMS_SQL. |
| index1 | The lower bound index for the bound table member.  |
| index2 | The upper bound index for the bound table member.   |

Description:

- The indices in index1 and index2 can be discontinuous.
- BIND_ARRAY and DEFINE_ARRAY cannot be used simultaneously.

***Example***

```plsql
DECLARE
  V_INT_TABLE DBMS_SQL.NUMBER_TABLE := DBMS_SQL.NUMBER_TABLE(1=>1, 2=>2);
  V_VARCHAR_TABLE DBMS_SQL.VARCHAR2_TABLE := DBMS_SQL.VARCHAR2_TABLE(1=>'Operation Dep', 2=>'QA Dep');
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
BEGIN
  V_SQL := 'INSERT INTO department VALUES(:1, :2)';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.BIND_ARRAY(C, ':1', V_INT_TABLE);
  DBMS_SQL.BIND_ARRAY(C, ':2', V_VARCHAR_TABLE);
  R := DBMS_SQL.EXECUTE(C);
  COMMIT;
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/


SELECT deparment_no,department_name FROM department;
DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep
1            Operation Dep
2            QA Dep


DELETE FROM department WHERE deparment_no IN ('1','2');
```

## DEFINE\_COLUMN

```plsql
DBMS_SQL.DEFINE_COLUMN (
   c              IN INTEGER,
   position       IN INTEGER,
   column         IN <datatype>);
```

or:

```plsql
DBMS_SQL.DEFINE_COLUMN (
   c              IN INTEGER,
   position       IN INTEGER,
   column         IN VARCHAR2,
   column_size    IN INTEGER);
```

DEFINE_COLUMN is used to define the position of the projection column for the given cursor.

This subprogram can only be used when the cursor is associated with a QUERY statement.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| position | The relative position of the column to define, of INTEGER type, starting with index 1. |
| column | The value defining the column, which determines the type of the defined column. |
| column_size | Expected size of the column value for VARCHAR type. |

Description:

- For CHAR or NCHAR type columns, column_size must be specified.
- If column_size is specified, it is treated as if column is of VARCHAR type.

***Example***

```plsql
SELECT deparment_no,department_name FROM department;
DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep


DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_INT INT;
  V_VARCHAR VARCHAR(100);
BEGIN
  V_SQL := 'SELECT * FROM department';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN(C, 1, V_INT);
  DBMS_SQL.DEFINE_COLUMN(C, 2, V_VARCHAR, 100);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE(C, 1, V_INT);
  DBMS_SQL.COLUMN_VALUE(C, 2, V_VARCHAR);
  DBMS_OUTPUT.PUT_LINE(V_INT || V_VARCHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
0Public Dep

```

## DEFINE\_COLUMN\_RAW

```plsql
DBMS_SQL.DEFINE_COLUMN_RAW (
   c              IN INTEGER,
   position       IN INTEGER,
   column         IN RAW,
   column_size    IN INTEGER);
```

DEFINE_COLUMN_RAW is used to define the position of the projection column for the given cursor, where the column value is of RAW type.

This subprogram can only be used when the cursor is associated with a QUERY statement.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| position | The relative position of the column to define, of INTEGER type, starting with index 1. |
| column | The value defining the column, which is of RAW type. |
| column_size | Expected size of the column value for RAW type. |

***Example***

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_RAW RAW(10);
BEGIN
  V_SQL := 'SELECT ''6364'' FROM dual';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN_RAW(C, 1, V_RAW, 10);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE_RAW(C, 1, V_RAW);
  DBMS_OUTPUT.PUT_LINE(V_RAW);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
6364

```

## DEFINE\_COLUMN\_CHAR

```plsql
DBMS_SQL.DEFINE_COLUMN_CHAR (
   c              IN INTEGER,
   position       IN INTEGER,
   column         IN CHAR,
   column_size    IN INTEGER);
```

DEFINE_COLUMN_CHAR is used to define the position of the projection column for the given cursor, where the column value is of CHAR type.

This subprogram can only be used when the cursor is associated with a QUERY statement.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| position | The relative position of the column to define, of INTEGER type, starting with index 1. |
| column | The value defining the column, which is of CHAR type. |
| column_size | Expected size of the column value for CHAR type. |

***Example***

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_CHAR CHAR(10);
BEGIN
  V_SQL := 'SELECT ''abc'' FROM dual';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN_CHAR(C, 1, V_CHAR, 10);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE_CHAR(C, 1, V_CHAR);
  DBMS_OUTPUT.PUT_LINE(V_CHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
abc       

```

## DEFINE\_ARRAY

```plsql
DBMS_SQL.DEFINE_ARRAY (
   c           IN INTEGER, 
   position    IN INTEGER,
   <table_variable>    IN <datatype> 
   cnt         IN INTEGER, 
   lower_bnd   IN INTEGER);
```

DEFINE_ARRAY is used to define the columns needed for FETCHing, allowing multiple rows of results from a SELECT statement to be fetched into nested table variables.

This subprogram can only be used when the cursor is associated with a QUERY statement.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| position | The relative position of the column to define, of INTEGER type, starting with index 1. |
| table_variable | Defined as a nested table type variable based on DBMS_SQL's built-in types. |
| cnt | The number of result set rows to be fetched.        |
| lower_bnd | The lower bound index position to copy results into the nested table variable. |

Description:

- When executing FETCH_ROWS, the result set is sent to cache, updating the current index position. COLUMN_VALUE then assigns the cached value to the variable and updates the lower bound index position.
- DEFINE_ARRAY and BIND_ARRAY cannot be used simultaneously.

***Example***

```plsql
SELECT deparment_no,department_name FROM department;
DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep


DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_NUMBER_TABLE DBMS_SQL.NUMBER_TABLE;
  V_VARCHAR_TABLE DBMS_SQL.VARCHAR2_TABLE;
BEGIN
  V_SQL := 'SELECT * FROM department';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_ARRAY(C, 1, V_NUMBER_TABLE, 2, 1);
  DBMS_SQL.DEFINE_ARRAY(C, 2, V_VARCHAR_TABLE, 2, 1);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE(C, 1, V_NUMBER_TABLE);
  DBMS_SQL.COLUMN_VALUE(C, 2, V_VARCHAR_TABLE);
  FOR I IN V_NUMBER_TABLE.FIRST .. V_NUMBER_TABLE.LAST LOOP
    DBMS_OUTPUT.PUT_LINE(i || ': ' || V_NUMBER_TABLE(i) || ', ' ||V_VARCHAR_TABLE(i));
  END LOOP;
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/
--result
1: 0, Public Dep
2: 10, Sales Dep

```

## EXECUTE

```plsql
DBMS_SQL.EXECUTE (
   c   IN INTEGER)
  RETURN INTEGER;
```

EXECUTE is used to execute a cursor statement and returns the number of processed rows, which is of INTEGER type. The return value is only valid for INSERT, UPDATE, and DELETE cursor statements.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |

***Example***

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
BEGIN
  V_SQL := 'SELECT * FROM dual';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  R := DBMS_SQL.EXECUTE(C);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

```

## FETCH\_ROWS

```plsql
DBMS_SQL.FETCH_ROWS (
   c              IN INTEGER)
  RETURN INTEGER;
```

FETCH_ROWS is used to fetch result sets and returns the actual number of rows fetched, which is of INTEGER type. This applies when the cursor is for DEFINE_ARRAY; multiple rows of results are fetched at once.

This subprogram can only be used when the cursor is associated with a QUERY statement.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |

***Example***

```plsql
SELECT deparment_no,department_name FROM department;
DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep


DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_INT INT;
  V_VARCHAR VARCHAR(100);
BEGIN
  V_SQL := 'SELECT * FROM department';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN(C, 1, V_INT);
  DBMS_SQL.DEFINE_COLUMN(C, 2, V_VARCHAR, 100);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE(C, 1, V_INT);
  DBMS_SQL.COLUMN_VALUE(C, 2, V_VARCHAR);
  DBMS_OUTPUT.PUT_LINE(V_INT || V_VARCHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
0Public Dep

```

## EXECUTE\_AND\_FETCH

```plsql
DBMS_SQL.EXECUTE_AND_FETCH (
   c              IN INTEGER,
   exact          IN BOOLEAN DEFAULT FALSE)
  RETURN INTEGER;
```

EXECUTE_AND_FETCH executes the given cursor and fetches the result set, returning the actual number of rows fetched, which is of INTEGER type.

This subprogram can only be used when the cursor is associated with a QUERY statement.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| exact | Specifies whether to check the number of result set rows. If TRUE, it checks if the returned result set is 1 row; otherwise, it raises an error. If FALSE, it does not check the returned result set row count. |

***Example***

```plsql
SELECT deparment_no,department_name FROM department;
DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep


DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_INT INT;
  V_VARCHAR VARCHAR(100);
BEGIN
  V_SQL := 'SELECT * FROM department';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN(C, 1, V_INT);
  DBMS_SQL.DEFINE_COLUMN(C, 2, V_VARCHAR, 100);
  R := DBMS_SQL.EXECUTE_AND_FETCH(C);
  DBMS_SQL.COLUMN_VALUE(C, 1, V_INT);
  DBMS_SQL.COLUMN_VALUE(C, 2, V_VARCHAR);
  DBMS_OUTPUT.PUT_LINE(V_INT || V_VARCHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
0Public Dep

```

## VARIABLE\_VALUE

```plsql
DBMS_SQL.VARIABLE_VALUE (
   c               IN  INTEGER,
   name            IN  VARCHAR2,
   value           OUT NOCOPY <datatype>);
```

VARIABLE_VALUE is used to retrieve the value of a bind variable.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| name | The name of the bind variable to retrieve the value; needs to match the variable name in the SQL statement, of VARCHAR type. |
| value | The variable to assign the value of the bind variable, which can be of any type supported by the SQL statement. |

Description:

- The leading colon in the name parameter can be omitted.
- The data type of value in VARIABLE_VALUE must match the data type of the bind variable during the binding phase.

***Example***

```plsql
INSERT INTO department VALUES('121', 'Service Dep');

DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_VARCHAR VARCHAR(100);
BEGIN
  V_SQL := 'SELECT * FROM department WHERE department_name = :B1';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.BIND_VARIABLE(C, ':B1', 'Service Dep');
  R := DBMS_SQL.EXECUTE(C);
  DBMS_SQL.VARIABLE_VALUE(C, 'B1', V_VARCHAR);
  DBMS_OUTPUT.PUT_LINE(V_VARCHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
Service Dep


DELETE FROM department WHERE deparment_no='121';
```

## VARIABLE\_VALUE\_RAW

```plsql
DBMS_SQL.VARIABLE_VALUE_RAW (
   c               IN  INTEGER,
   name            IN  VARCHAR2,
   value           OUT RAW);
```

VARIABLE_VALUE_RAW is used to retrieve the value of a RAW type bind variable.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| name | The name of the bind variable to retrieve the value; needs to match the variable name in the SQL statement, of VARCHAR type. |
| value | The variable to assign the value of the bind variable, which is of RAW type. |

***Example***

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_RAW RAW(10) := '6364';
BEGIN
  V_SQL := 'begin :B1 := ''6566''; end;';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.BIND_VARIABLE_RAW(C, ':B1', V_RAW, 10);
  R := DBMS_SQL.EXECUTE(C);
  DBMS_SQL.VARIABLE_VALUE_RAW(C, 'B1', V_RAW);
  DBMS_OUTPUT.PUT_LINE(V_RAW);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
6566

```

## VARIABLE\_VALUE\_CHAR

```plsql
DBMS_SQL.VARIABLE_VALUE_CHAR (
   c               IN  INTEGER,
   name            IN  VARCHAR2,
   value           OUT CHAR);
```

VARIABLE_VALUE_CHAR is used to retrieve the value of a CHAR type bind variable.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| name | The name of the bind variable to retrieve the value; needs to match the variable name in the SQL statement, of VARCHAR type. |
| value | The variable to assign the value of the bind variable, which is of CHAR type. |

***Example***

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_CHAR CHAR(10) := 'abc';
BEGIN
  V_SQL := 'begin :B1 := ''defg''; end;';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.BIND_VARIABLE_CHAR(C, ':B1', V_CHAR, 10);
  R := DBMS_SQL.EXECUTE(C);
  DBMS_SQL.VARIABLE_VALUE_CHAR(C, 'B1', V_CHAR);
  DBMS_OUTPUT.PUT_LINE(V_CHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
defg      

```

## COLUMN\_VALUE

```plsql
DBMS_SQL.COLUMN_VALUE (
   c                 IN  INTEGER,
   position          IN  INTEGER,
   value             OUT <datatype> 
 [,column_error      OUT NUMBER] 
 [,actual_length     OUT INTEGER]);
```

COLUMN_VALUE retrieves the value of a specified column in the result set fetched by the cursor and assigns it to value.

This subprogram can only be used when the cursor is associated with a QUERY statement.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| position | The relative position of the projected column, of INTEGER type, starting with index 1. |
| value | Assigns the value of the specified column in the result set to value, which can be of any type supported by the SQL statement. |
| column_error | Syntax compatible.                               |
| actual_length | Returns the actual length before column truncation. |

***Example***

```plsql
SELECT deparment_no,department_name FROM department;
DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep


DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_INT INT;
  V_VARCHAR VARCHAR(100);
BEGIN
  V_SQL := 'SELECT * FROM department';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN(C, 1, V_INT);
  DBMS_SQL.DEFINE_COLUMN(C, 2, V_VARCHAR, 100);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE(C, 1, V_INT);
  DBMS_SQL.COLUMN_VALUE(C, 2, V_VARCHAR);
  DBMS_OUTPUT.PUT_LINE(V_INT || V_VARCHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
0Public Dep

```

## COLUMN\_VALUE\_RAW

```plsql
DBMS_SQL.COLUMN_VALUE_RAW (
   c                 IN  INTEGER,
   position          IN  INTEGER,
   value             OUT RAW 
 [,column_error      OUT NUMBER] 
 [,actual_length     OUT INTEGER]);
```

COLUMN_VALUE_RAW retrieves the RAW type value of a specified column in the result set fetched by the cursor and assigns it to value.

This subprogram can only be used when the cursor is associated with a QUERY statement.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| position | The relative position of the projected column, of INTEGER type, starting with index 1. |
| value | Assigns the value of the specified column in the result set to value, which is of RAW type. |
| column_error | Syntax compatible.                               |
| actual_length | Returns the actual length before column truncation. |

***Example***

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_RAW RAW(10);
BEGIN
  V_SQL := 'SELECT ''6364'' FROM dual';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN_RAW(C, 1, V_RAW, 10);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE_RAW(C, 1, V_RAW);
  DBMS_OUTPUT.PUT_LINE(V_RAW);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
6364

```

## COLUMN\_VALUE\_CHAR

```plsql
DBMS_SQL.COLUMN_VALUE_CHAR (
   c                 IN  INTEGER,
   position          IN  INTEGER,
   value             OUT CHAR 
 [,column_error      OUT NUMBER] 
 [,actual_length     OUT INTEGER]);
```

COLUMN_VALUE_CHAR retrieves the CHAR type value of a specified column in the result set fetched by the cursor and assigns it to value.

This subprogram can only be used when the cursor is associated with a QUERY statement.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| position | The relative position of the projected column, of INTEGER type, starting with index 1. |
| value | Assigns the value of the specified column in the result set to value, which is of CHAR type. |
| column_error | Syntax compatible.                               |
| actual_length | Returns the actual length before column truncation. |

***Example***

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_CHAR CHAR(10);
BEGIN
  V_SQL := 'SELECT ''abc'' FROM dual';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN_CHAR(C, 1, V_CHAR, 10);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE_CHAR(C, 1, V_CHAR);
  DBMS_OUTPUT.PUT_LINE(V_CHAR);
  DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
abc       

```

## IS\_OPEN

```plsql
DBMS_SQL.IS_OPEN (
   c              IN INTEGER)
  RETURN BOOLEAN;
```

IS_OPEN is used to determine whether the cursor is in an open state. The return value is of BOOLEAN type; if open, it returns TRUE; if the input parameter is NULL, it returns FALSE.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be checked, of INTEGER type.       |

***Example***

```plsql
DECLARE
  C INTEGER;
  IS_OPEN BOOLEAN;
BEGIN
  C := DBMS_SQL.OPEN_CURSOR();
  IS_OPEN := DBMS_SQL.IS_OPEN(C);
  DBMS_OUTPUT.PUT_LINE(IS_OPEN);
  DBMS_SQL.CLOSE_CURSOR(C);
  IS_OPEN := DBMS_SQL.IS_OPEN(C);
  DBMS_OUTPUT.PUT_LINE(IS_OPEN);
END;
/

--result
true
false

```

## TO\_REFCURSOR

```plsql
DBMS_SQL.TO_REFCURSOR(
   cursor_number IN OUT INTEGER)
  RETURN SYS_REFCURSOR;
```

TO_REFCURSOR converts an open, parsed, and executed cursor into a SYS_REFCURSOR and returns the resulting SYS_REFCURSOR.

|Parameter |Description |
| :---- |:-----------|
| cursor_number | The cursor ID to be converted, of INTEGER type.<br/> Upon successful execution, can act as an output parameter and will be automatically set to NULL. |

***Example***

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_INT INT;
  C_REF SYS_REFCURSOR;
BEGIN
  V_SQL := 'SELECT 1 FROM DUAL';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  R := DBMS_SQL.EXECUTE(C);
  C_REF := DBMS_SQL.TO_REFCURSOR(C);
  FETCH C_REF INTO V_INT;
  DBMS_OUTPUT.PUT_LINE(V_INT);
END;
/

--result
1

```

## TO\_CURSOR\_NUMBER

```plsql
DBMS_SQL.TO_CURSOR_NUMBER(
   rc IN OUT SYS_REFCURSOR)
  RETURN INTEGER;
```

TO_CURSOR_NUMBER converts an open dynamic cursor into a cursor managed by DBMS_SQL and returns the resulting cursor ID.

The converted DBMS_SQL cursor is in an executed state and cannot be executed again.

|Parameter |Description |
| :---- |:-----------|
| rc | The dynamic cursor to be converted, of SYS_REFCURSOR type. The dynamic cursor rc will be set to an invalid cursor after conversion. |

***Example***

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_INT INT;
  C_REF SYS_REFCURSOR;
BEGIN
  V_SQL := 'SELECT 1 FROM DUAL';
  OPEN C_REF FOR V_SQL;
  C := DBMS_SQL.TO_CURSOR_NUMBER(C_REF);
  DBMS_SQL.DEFINE_COLUMN(C, 1, V_INT);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE(C, 1, V_INT);
  DBMS_OUTPUT.PUT_LINE(V_INT);
END;
/

--result
1

```

## DESCRIBE\_COLUMNS

```plsql
DBMS_SQL.DESCRIBE_COLUMNS ( 
   c              IN  INTEGER, 
   col_cnt        OUT INTEGER, 
   desc_t         OUT DESC_TAB);
```

DESCRIBE_COLUMNS describes the projection columns of the cursor opened and parsed by DBMS_SQL.

|Parameter |Description |
| :---- |:-----------|
| c | The cursor ID to be used, of INTEGER type.          |
| col_cnt | Returns the number of projection columns.            |
| desc_t | Returns the description information for each projection column. |

***Example***

```plsql
DECLARE
V_STR VARCHAR(100);
C INT;
COL_CNT INT;
REC_TABLE DBMS_SQL.DESC_TAB;
REC DBMS_SQL.DESC_REC;
R NUMBER;
COL_NUM NUMBER;
BEGIN
V_STR := 'SELECT deparment_no, department_name FROM department';
C := DBMS_SQL.OPEN_CURSOR;
DBMS_SQL.PARSE(C, V_STR, 1);
DBMS_SQL.DESCRIBE_COLUMNS(C, COL_CNT, REC_TABLE);
COL_NUM := REC_TABLE.FIRST;
WHILE COL_NUM IS NOT NULL LOOP
	REC := REC_TABLE(COL_NUM);
	DBMS_OUTPUT.PUT_LINE('----COL: ' || COL_NUM || '----');
    DBMS_OUTPUT.PUT_LINE('COL_TYPE            =    ' || REC.COL_TYPE);
    DBMS_OUTPUT.PUT_LINE('COL_MAXLEN          =    ' || REC.COL_MAX_LEN);
    DBMS_OUTPUT.PUT_LINE('COL_NAME            =    ' || REC.COL_NAME);
    DBMS_OUTPUT.PUT_LINE('COL_NAME_LEN        =    ' || REC.COL_NAME_LEN);
    DBMS_OUTPUT.PUT_LINE('COL_SCHEMA_NAME     =    ' || REC.COL_SCHEMA_NAME);
    DBMS_OUTPUT.PUT_LINE('COL_SCHEMA_NAME_LEN =    ' || REC.COL_SCHEMA_NAME_LEN);
    DBMS_OUTPUT.PUT_LINE('COL_PRECISION       =    ' || REC.COL_PRECISION);
    DBMS_OUTPUT.PUT_LINE('COL_SCALE           =    ' || REC.COL_SCALE);
	  DBMS_OUTPUT.PUT_LINE('COL_CHARSETID       =    ' || REC.COL_CHARSETID);
	  DBMS_OUTPUT.PUT_LINE('COL_CHARSETFORM     =    ' || REC.COL_CHARSETFORM);
	  DBMS_OUTPUT.PUT_LINE('COL_NULL_OK         =    ' || REC.COL_NULL_OK);
	COL_NUM := REC_TABLE.NEXT(COL_NUM);
END LOOP;
DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result
----COL: 1----
COL_TYPE            =    24
COL_MAXLEN          =    3
COL_NAME            =    DEPARMENT_NO
COL_NAME_LEN        =    12
COL_SCHEMA_NAME     =
COL_SCHEMA_NAME_LEN =    0
COL_PRECISION       =    0
COL_SCALE           =    0
COL_CHARSETID       =    2
COL_CHARSETFORM     =
COL_NULL_OK         =    false
----COL: 2----
COL_TYPE            =    26
COL_MAXLEN          =    20
COL_NAME            =    DEPARTMENT_NAME
COL_NAME_LEN        =    15
COL_SCHEMA_NAME     =
COL_SCHEMA_NAME_LEN =    0
COL_PRECISION       =    0
COL_SCALE           =    0
COL_CHARSETID       =    2
COL_CHARSETFORM     =
COL_NULL_OK         =    true

```

## LAST\_ERROR\_POSITION

```plsql
DBMS_SQL.LAST_ERROR_POSITION 
   RETURN INTEGER;
```

Returns the byte offset position of the SQL syntax error, starting from 0.

This can only be obtained if an error occurs during DBMS_SQL.PARSE or DBMS_SQL.EXECUTE phases and the original error message contains position information.

***Example***

```plsql
DECLARE
C1 INT;
V_SQL VARCHAR(100);
BEGIN
C1 := DBMS_SQL.OPEN_CURSOR();
V_SQL := 'SELECT X FROM DUAL';
DBMS_SQL.PARSE(C1, V_SQL, DBMS_SQL.NATIVE);
EXCEPTION WHEN OTHERS THEN
DBMS_OUTPUT.PUT_LINE(DBMS_SQL.LAST_ERROR_POSITION || ', ' || SQLERRM);
END;
/

--result
7, YAS-04243 invalid identifier "X"
```

## RETURN\_RESULT

```plsql
DBMS_SQL.RETURN_RESULT (
rc              IN OUT SYS_REFCURSOR,
to_client       IN BOOLEAN DEFAULT TRUE);
```

RETURN_RESULT is a stored procedure used to execute a specified SQL and return a result set to the client (for example, interface programs). After the client obtains the result set cursor, it can execute fetch statements to query results.

When executed in *yasql*, it directly outputs the content of the result set.

|Parameter |Description |
| --------- | ------------------------------------------------------------ |
| rc        | Dynamic cursor.                                      |
| to_client | Specifies whether to return the result set to the client; default is true; passing false means the result set will not be returned to the client when executing dynamic SQL. |

> **Note**: 
>
> - Currently, only query results can be returned.
> - After executing to return a result set, the cursor corresponding to the rc parameter cannot be accessed.
> - The client buffers all results of the result set; however, it relies on the memory size of the device where the client is located.
> - If an error occurs during the procedure execution, the client can still retrieve the result set returned before the error point.

***Example***

```sql
CREATE OR REPLACE PROCEDURE return_rs_proc IS
cur1 SYS_REFCURSOR;
cur2 sys_refcursor;
BEGIN
  OPEN cur1 FOR SELECT area_no,area_name FROM area WHERE area_no='01';
  DBMS_SQL.RETURN_RESULT(cur1,false);
  OPEN cur2 FOR SELECT branch_no,branch_name FROM branches WHERE area_no='01';
  DBMS_SQL.RETURN_RESULT(cur2);
END;
/

-- Execute static SQL
exec return_rs_proc;

ResultSet #1

AREA_NO AREA_NAME                                                     
------- ------------------------------------------------------------- 
01      EastChina                                                       


ResultSet #2

BRANCH_NO BRANCH_NAME                                                      
--------- ---------------------------------------------------------------- 
0101      Shanghai                                                          
0102      Nanjing                                                          
0103      Fuzhou                                                          
0104      Xiamen                                                          


-- Execute dynamic SQL; since to_client is false, the first result set won't return
BEGIN
  EXECUTE IMMEDIATE 'begin return_rs_proc; end;';
END;
/

ResultSet #1

BRANCH_NO BRANCH_NAME                                                      
--------- ---------------------------------------------------------------- 
0101      Shanghai                                                          
0102      Nanjing                                                          
0103      Fuzhou                                                          
0104      Xiamen                                                          

```

## Constants

|Constant |Data Type |Value |Description |
| :---- | :---- | :---- | :---- |
| NATIVE   | INTEGER   | 1    | Specifies normal behavior for the program connection. |

## Exceptions

|Exception |Description |
| :---- | :---- |
| INCONSISTENT_TYPE | This exception is raised when the OUT argument type given in COLUMN_VALUE or VARIABLE_VALUE subprograms does not match the previously defined type. |

***Example***

```plsql
DECLARE
  C INTEGER;
  R INTEGER;
  V_SQL VARCHAR(32000);
  V_INT INT;
  V_VARCHAR VARCHAR(100);
BEGIN
  V_SQL := 'SELECT 1 FROM DUAL';
  C := DBMS_SQL.OPEN_CURSOR();
  DBMS_SQL.PARSE(C, V_SQL, DBMS_SQL.NATIVE);
  DBMS_SQL.DEFINE_COLUMN(C, 1, V_INT);
  R := DBMS_SQL.EXECUTE(C);
  R := DBMS_SQL.FETCH_ROWS(C);
  DBMS_SQL.COLUMN_VALUE(C, 1, V_VARCHAR);
  EXCEPTION WHEN DBMS_SQL.INCONSISTENT_TYPE THEN
    DBMS_OUTPUT.PUT_LINE('type of out argument must match type of column or bind variable');
    DBMS_SQL.CLOSE_CURSOR(C);
END;
/

--result 
type of out argument must match type of column or bind variable

```

## DBMS\_SQL DESC\_REC Record Type

As a member type of DESC_TAB nested table type, it describes the projection column information.

```plsql
TYPE desc_rec IS RECORD (
      col_type            BINARY_INTEGER := 0,
      col_max_len         BINARY_INTEGER := 0,
      col_name            VARCHAR2(64)   := '',
      col_name_len        BINARY_INTEGER := 0,
      col_schema_name     VARCHAR2(64)   := '',
      col_schema_name_len BINARY_INTEGER := 0,
      col_precision       BINARY_INTEGER := 0,
      col_scale           BINARY_INTEGER := 0,
      col_charsetid       BINARY_INTEGER := 0,
      col_charsetform     BINARY_INTEGER := 0,
      col_null_ok         BOOLEAN        := TRUE);
TYPE desc_tab IS TABLE OF desc_rec INDEX BY BINARY_INTEGER;
```

|Field |Type |Default Value |Description |
| :---- | :---- | :---- | :---- |
| col_type              | BINARY_INTEGER    | 0            | Column type.                     |
| col_max_len           | BINARY_INTEGER    | 0            | Maximum column length.           |
| col_name              | VARCHAR2(64)      | NULL         | Column name.                     |
| col_name_len          | BINARY_INTEGER    | 0            | Column name length.              |
| col_schema_name       | VARCHAR2(64)      | NULL         | Column schema name.              |
| col_schema_name_len   | BINARY_INTEGER    | 0            | Column schema name length.       |
| col_precision         | BINARY_INTEGER    | 0            | Column precision.                |
| col_scale             | BINARY_INTEGER    | 0            | Column scale.                    |
| col_charsetid         | BINARY_INTEGER    | 0            | Column character string ID.      |
| col_charsetform       | BINARY_INTEGER    | 0            | Column character set format (reserved field). |
| col_null_ok           | BOOLEAN           | TRUE         | Whether the column can be NULL, TRUE means it can be NULL. |

## DBMS\_SQL DESC\_TAB Table Type

Used to store the column description information obtained from describe_column.

```plsql
TYPE desc_tab IS TABLE OF desc_rec INDEX BY BINARY_INTEGER;
```

## DBMS\_SQL TABLE Types For Scalar and LOB Collections

Definitions for nested tables of scalar and LOB types.

```plsql
TYPE binary_double_table IS TABLE OF BINARY_DOUBLE  INDEX BY BINARY_INTEGER;
TYPE binary_float_table IS TABLE OF BINARY_FLOAT   INDEX BY BINARY_INTEGER;
TYPE blob_table     IS TABLE OF BLOB           INDEX BY BINARY_INTEGER;
TYPE clob_table     IS TABLE OF CLOB           INDEX BY BINARY_INTEGER;
TYPE date_table     IS TABLE OF DATE           INDEX BY BINARY_INTEGER;
TYPE interval_day_to_second_table IS TABLE OF INTERVAL DAY TO SECOND INDEX BY BINARY_INTEGER;
TYPE interval_year_to_month_table IS TABLE OF INTERVAL YEAR TO MONTH INDEX BY BINARY_INTEGER;
TYPE number_table   IS TABLE OF NUMBER         INDEX BY BINARY_INTEGER;
TYPE timestamp_table IS TABLE OF timestamp INDEX BY BINARY_INTEGER;
TYPE time_table IS TABLE OF TIME INDEX BY BINARY_INTEGER;
TYPE urowid_table IS TABLE OF UROWID INDEX BY BINARY_INTEGER;
TYPE varchar2_table IS TABLE OF VARCHAR2(65534) INDEX BY BINARY_INTEGER;
```
