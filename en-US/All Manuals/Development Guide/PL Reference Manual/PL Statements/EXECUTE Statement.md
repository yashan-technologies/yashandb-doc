The EXECUTE Statement is a dynamic SQL execution statement, and its conventional syntax is:

*EXECUTE IMMEDIATE v_sql;*

Here, v_sql is a string variable or constant in the form of a SQL statement. In YashanDB, v_sql can be not only a standard SQL statement but also an anonymous block in the form of "BEGIN..END;".

## Static SQL vs. Dynamic SQL

Static SQL: SQL statements that are defined when the procedure body is created, such as [DML Statement](DML Statement) and [DCL Statement](DCL Statement).

Dynamic SQL: SQL statements that are determined only when the procedure body is executed, such as fields and conditions from user input, or those which can only be dynamically constructed according to program logic.

In the EXECUTE Statement, v_sql is dynamic SQL.

## General Usage

*EXECUTE IMMEDIATE v_sql;*

Executes the SQL statement contained in the v_sql string.

***Example***

```plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
BEGIN
EXECUTE IMMEDIATE 'alter table area drop DHQ';
END;
/
exec ya_proc;
```

## INTO Usage

*EXECUTE IMMEDIATE v_sql INTO v1,v2,...;*

'v1,v2...' are declared variables.

Executes the SQL statement contained in the v_sql string (only SELECT statements), and assigns the query results to the variables; 'v1,v2...' correspond one-to-one with the selected items in the SELECT statement.

***Example***

```plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
no VARCHAR(10);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where rownum=1';
BEGIN
EXECUTE IMMEDIATE str1 INTO no,name ;
DBMS_OUTPUT.PUT_LINE('AREANO:'||no||'AREANAME:'||name);
END;
/
exec ya_proc;
 
--result
AREANO:AREANAME:EastChina
```

## USING Usage

*EXECUTE IMMEDIATE v_sql  USING \[IN|OUT|IN OUT\] a1,a2,...;*

a1,a2... are variables or constants as [bind parameters](../Parameters/Binding Parameters).

> **Note**: 
> If v_sql is an anonymous block and uses `:name` form placeholders, the same placeholder only needs to be bound once.

IN|OUT|IN OUT indicates the type of bind parameters, defaulting to IN type. The rules are as follows:

*   IN parameters can be variables, variable expressions, or constants;
*   OUT and IN OUT parameters can only be declared variables;
*   When v_sql includes a call to another procedure, the type of bind parameters must correspond with the defined parameter types of that procedure.

When USING and INTO are used together, the INTO statement should precede the USING statement.

***Example***

```plsql
--v_sql is a DML statement
CREATE OR REPLACE PROCEDURE ya_proc() IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no in (:a,:b) and rownum=1';
BEGIN
EXECUTE IMMEDIATE str1 INTO no,name USING '01','02';
DBMS_OUTPUT.PUT_LINE('AREANO:'||no||'AREANAME:'||name);
END;
/
exec ya_proc;
 
--result
AREANO:AREANAME:EastChina
```

***Example*** for Heap tables

```plsql
--insert into...return into...
CREATE OR REPLACE PROCEDURE ya_proc IS
no INT;
area_name VARCHAR(20);
str1 VARCHAR(200) := 'INSERT INTO area(area_no) VALUES(:a) RETURN TO_NUMBER(area_no), NVL(area_name, ''area''||area_no) INTO :b, :c';
BEGIN
EXECUTE IMMEDIATE str1 USING '09',out no,out area_name;
DBMS_OUTPUT.PUT_LINE('AREANO:'||no||'AREANAME:'||area_name);
END;
/
exec ya_proc;
 
--result
AREANO:AREANAME:area09
```

***Example***

```plsql
--v_sql is a procedure call statement
--Define a stored procedure where a2's parameter is assigned a value in the procedure body
CREATE OR REPLACE PROCEDURE ya_block(a1 VARCHAR,a2 IN OUT VARCHAR) IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no in (:a,:b) and rownum=1';
BEGIN
EXECUTE IMMEDIATE str1 INTO no,name USING a1,a2;
DBMS_OUTPUT.PUT_LINE('AREANO:'||no||'AREANAME:'||name);
a2 := '00';
END;
/
--Calling through an anonymous block
DECLARE
a1 VARCHAR(2):='01';
a2 VARCHAR(2):='02';
BEGIN
DBMS_OUTPUT.PUT_LINE('a1:'||a1||',a2:'||a2);
ya_block(a1,a2);
DBMS_OUTPUT.PUT_LINE('a1:'||a1||',a2:'||a2);
END;
/
--result
a1:01,a2:02
AREANO:AREANAME:EastChina
a1:01,a2:00
 
--Calling through dynamic SQL
CREATE OR REPLACE PROCEDURE ya_proc IS
a1 VARCHAR(2):='01';
a2 VARCHAR(2):='02';
BEGIN
DBMS_OUTPUT.PUT_LINE('a1:'||a1||',a2:'||a2);
EXECUTE IMMEDIATE 'begin ya_block(:a,:b); end;' USING a1,IN OUT a2;
DBMS_OUTPUT.PUT_LINE('a1:'||a1||',a2:'||a2);
END;
/
exec ya_proc;
--result
a1:01,a2:02
AREANO:AREANAME:EastChina
a1:01,a2:00
```

## RETURNING Usage

For the RETURING syntax, please refer to [UPDATE](../../SQL参考手册/SQL语句（yashan模式）/UPDATE.html#returningclause).

### RETURNING INTO Usage

*EXECUTE IMMEDIATE v_sql RETURNING INTO v1,v2,...;*

`v1,v2...` are declared variables.

Executes the SQL statement contained in the v_sql string (can only be UPDATE statements) and assigns the results of the expressions specified in the RETURNING clause of v_sql to the variables; `v1,v2...` correspond one-to-one with the RETURNING items in the UPDATE statement.

When USING and RETURNING are used together, the USING statement should precede the RETURNING statement.

***Example*** for Heap tables

```plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
  no VARCHAR(10);
  name VARCHAR(20);
  str1 VARCHAR(100) := 'update area set area_no = ''01'' where area_no = :a returning area_no,area_name into :b,:c';
BEGIN
  EXECUTE IMMEDIATE str1 USING '01' RETURNING INTO no, name;
  DBMS_OUTPUT.PUT_LINE('AREANO:'||no||'AREANAME:'||name);
END;
/

exec ya_proc;

*--result*
AREANO:AREANAME:EastChina
```

### RETURNING BULK COLLECT INTO Usage

*EXECUTE IMMEDIATE v_sql RETURNING BULK COLLECT INTO v1,v2,...;*

`v1,v2...` are declared collection variables.

Executes the SQL statement contained in the v_sql string (can only be UPDATE statements, "BULK COLLECT" cannot be used in the UPDATE statement) and assigns the results of the returning expressions in v_sql to the collection variables; `v1,v2...` correspond one-to-one with the RETURNING items in the UPDATE statement.

***Example*** for Heap tables

```plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
  TYPE area_tb IS TABLE OF area.area_name%TYPE;
  type_001 area_tb;
  str1 VARCHAR(100) := 'update area set area_no = area_no returning area_name into :a';
BEGIN
  EXECUTE IMMEDIATE str1 RETURNING BULK COLLECT INTO type_001;
  FOR i IN type_001.first..type_001.last LOOP
​    DBMS_OUTPUT.PUT_LINE('type_001('||i||'): ' || type_001(i) );
  END LOOP;
END;
/

exec ya_proc;

*--result*
type_001(1): EastChina
type_001(2): WestChina
type_001(3): SouthChina
type_001(4): NorthChina
type_001(5): CentralChina
```
