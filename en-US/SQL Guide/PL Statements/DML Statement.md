DML Statement is a static SQL execution statement, whose syntax form is consistent with conventional DML SQL statements, and it can be combined with the INTO clause to assign values to variables or bind parameters in PL.

Within the procedure body, DML statements can be executed directly or invoked via the [EXECUTE Statement](./EXECUTE Statement). In both cases, the INTO clause can still be used.

## Regular Usage

Static SQL cannot be a SELECT statement when not combined with INTO.

***Example***

```Plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
no VARCHAR(10);
name VARCHAR(20);
BEGIN
UPDATE area SET area_no='00' WHERE area_no='03';  
DELETE FROM area WHERE area_no='03'; 
INSERT INTO area 
SELECT area_no+20, area_name, dhq 
FROM area 
WHERE area_no IN ('01','02','03');
COMMIT;      
END;
/

exec ya_proc;
```

## select...into

This statement can assign the result of a SELECT statement from the database to variables, but the result can only be a single row, not zero or multiple rows.

For SELECT statements where a single result cannot be guaranteed, a [cursor](../../All Manuals/Development Guide/PL Reference Manual/PL Language Fundamentals/Variables/Cursors) must be used for result set assignment.

When executing a SELECT INTO statement, if a TOO MANY ROWS exception occurs, the first row has already been assigned to the variable before the exception is raised.

***Example***

```plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
no VARCHAR(10);
name VARCHAR(20);
BEGIN
SELECT area_no,area_name INTO no,name FROM area WHERE area_no='01';
DBMS_OUTPUT.PUT_LINE('AREANO:'||no||'AREANAME:'||name);
 
-- The FOLLOWING statement will throw a NO_DATA_FOUND EXCEPTION, captured BY the EXCEPTION Statement
SELECT area_no,area_name INTO no,name FROM area WHERE area_no='09';   
DBMS_OUTPUT.PUT_LINE('AREANO:'||no||'AREANAME:'||name);
EXCEPTION
WHEN NO_DATA_FOUND THEN
DBMS_OUTPUT.PUT_LINE('warning:no data found!');
END;
/
  
exec ya_proc;
  
--result
AREANO:AREANAME:EastChina
warning:no data found!
```

## select...bulk collect into

This statement can assign the results of a SELECT statement queried from the database to a collection variable, with the result allowing for zero rows or any number of rows.

Associative arrays using string types as keys do not allow the use of the BULK COLLECT clause.

The target object of BULK COLLECT INTO must be a collection type.

***Example***

```plsql

DECLARE
  TYPE area_tb IS TABLE OF area%rowtype;
  type_001 area_tb;
BEGIN
  SELECT * BULK COLLECT INTO type_001 FROM area ORDER BY 1,2;
  FOR i IN type_001.first..type_001.last LOOP
    DBMS_OUTPUT.PUT_LINE('type_001('||i||'): ' || type_001(i).area_no  || CHR(9) || type_001(i).area_name );
  END LOOP;
END;
/

--result
type_001(1): 01 EastChina
type_001(2): 02 WestChina
type_001(3): 03 SouthChina
type_001(4): 04 NorthChina
type_001(5): 05 CentralChina
```

## insert into...return...into

This statement allows for an INSERT operation with a specified result set to be returned and assigned to variables. RETURN can also be written as RETURNING.

The result set can contain one or more columns of data, each column being a [YashanDB general expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), but cannot include sequence, pseudocolumns, aggregate functions, window functions, or subqueries.

Under this usage, the INSERT statement can only use the [insert_values_clause](../../All Manuals/Development Guide/SQL Reference Manual/SQL Statements/INSERT) syntax, not for multi-row or subquery inserts.

In this usage, the [returning_clause](../../All Manuals/Development Guide/SQL Reference Manual/SQL Statements/INSERT) syntax cannot return variables.

LSC tables cannot use the insert into...return...into statement.

***Example*** for Heap tables

```plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
  no INT;
  v_area_name VARCHAR(20);
BEGIN
  INSERT INTO area(area_no) VALUES('09') 
  RETURN TO_NUMBER(area_no), NVL(area_name, 'area'||area_no) INTO no, v_area_name;
  DBMS_OUTPUT.PUT_LINE('AREANO:'||no||'AREANAME:'||v_area_name);
END;
/

exec ya_proc;
  
--result
AREANO:AREANAME:area09
```

## update...return...into

This statement allows for an UPDATE operation with a specified result set to be returned and assigned to variables. RETURN can also be written as RETURNING.

The result set can contain one or more columns of data, each column being a [YashanDB general expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), but cannot include sequence, pseudocolumns, aggregate functions, window functions, variables, or subqueries.

In this usage, the [returning_clause](../../All Manuals/Development Guide/SQL Reference Manual/SQL Statements/UPDATE.html#returningclause) syntax cannot return variables.

LSC tables cannot use the update...return...into statement; multi-table updates cannot use the update...return...into statement.

***Example*** for Heap tables

```plsql

CREATE OR REPLACE PROCEDURE ya_proc IS
  no INT;
  v_area_name VARCHAR(20);
BEGIN
  UPDATE area SET area_no = '09' WHERE area_no = '09'
  RETURN TO_NUMBER(area_no), NVL(area_name, 'area'||area_no) INTO no, v_area_name;
  DBMS_OUTPUT.PUT_LINE('AREANO:'||no||'AREANAME:'||v_area_name);
END;
/

exec ya_proc;

*--result*
AREANO:AREANAME:area09
```

## update...return...bulk collect into

This statement first executes the UPDATE statement, then queries the updated results from the database and assigns them to a collection variable; the result can be zero rows or any number of rows.

The target object of BULK COLLECT INTO must be a collection type.

***Example*** for Heap tables

```plsql

DECLARE
  TYPE area_tb IS TABLE OF area%rowtype;
  type_001 area_tb;
BEGIN
  UPDATE area SET area_no = area_no RETURNING area_no, area_name, dhq BULK COLLECT INTO type_001;
  FOR i IN type_001.first..type_001.last LOOP
​    DBMS_OUTPUT.PUT_LINE('type_001('||i||'): ' || type_001(i).area_no  || CHR(9) || type_001(i).area_name );
  END LOOP;
END;
/

*--result*
type_001(1): 01 EastChina
type_001(2): 02 WestChina
type_001(3): 03 SouthChina
type_001(4): 04 NorthChina
type_001(5): 05 CentralChina
```
