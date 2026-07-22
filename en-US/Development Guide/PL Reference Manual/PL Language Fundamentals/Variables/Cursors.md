A cursor is used to point to a row or multiple rows of results from a SQL statement, providing the ability to browse data row by row in the result set.

In YashanDB, user-declared and used cursors can be categorized as follows:

- Explicit Cursors: Cursors defined using the CURSOR...IS command, which statically binds a SQL statement at the time of definition and cannot be changed or reassigned.

- Dynamic Cursors: Cursor variables defined using TYPE...IS REF CURSOR, which can dynamically bind to different SQL statements (only one SQL statement can be bound at a time). The returned types of these SQL statements must be compatible with the defined type of the cursor variable.

- sys_refcursor: A system predefined dynamic cursor type that can be called directly, with usage consistent with dynamic cursors.

In YashanDB, the constraints for using cursors are as follows:

The number of cursors that can be opened per session in YashanDB is limited by the configuration parameter [OPEN_CURSORS](../../../../Reference Manual/Configuration Parameters), with a default value of 310.

- When the SQL bound to the cursor contains a FOR UPDATE statement, dblink is not supported. Other constraints are the same as those in [Limitations at the SQL Level](../../../SQL Reference Manual/SQL Statements/SELECT.md#forupdateclause), for example, in a multi-table join query, only the table corresponding to the column specified by OF column_name is updated. 

- When using PL variables and identifiers in the projection columns of the cursor's SQL statement, aliases must be specified for subsequent access.

- After a cursor is opened, any changes to the data in the referenced tables through transaction operations may lead to unstable execution results for the cursor fetch statements (it may succeed returning the data as of when the cursor was opened or fail with an error).

- Cursors cannot be used in ISC Distributed Cluster Deployment, but support for implicit cursor-related attributes is provided.

## Explicit Cursors

Explicit cursors are declared using cursor definition statements and can then perform operations such as [opening a cursor](../../PL Statements/OPEN Statement), [fetching from a cursor](../../PL Statements/FETCH Statement), and [closing a cursor](../../PL Statements/CLOSE Statement) within the procedure body.

**cursor_definition::=**

```ebnf
= CURSOR cursor ["(" cursor_parameters {"," cursor_parameters} ")"] [RETURN rowtype] IS select_statement ";".
```

**cursor_parameters**

Defines the parameters of the explicit cursor, which provide variable input for the bound SQL statement. The parameter definition format is:

*Parameter Name  Data Type  [Default Value]*

The data type should not include length, precision, or other attributes.

A default value can be defined for the cursor's parameters, allowing users to omit input values when opening the cursor. The system will use the default value as the parameter value. If an input value is provided, that value will be used.

When there are multiple cursor parameters, defining default values should be placed at the end; however, when passing parameter values using "=>", this order rule does not apply.

**RETURN rowtype**

Defines the return value of the explicit cursor, which must be specified as a RECORD type variable, and its columns must match the queried columns of the bound SQL statement.

The RETURN rowtype statement can be omitted, in which case the system will implicitly generate a return value variable whose column items match the queried column items of the bound SQL statement.

**select_statement**

A [SELECT](../../../SQL Reference Manual/SQL Statements/SELECT) statement that does not allow specifying placeholders (bind parameters). This section can utilize the INTO clause for compatibility purposes, but it has no actual effect.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
DECLARE 
  CURSOR cur(c1 CHAR) IS
  SELECT area_no, area_name FROM area WHERE area_no=c1;
  TYPE record1 IS RECORD (
    c1 CHAR(2),
	c2 VARCHAR(20)
  );
  rec record1;
BEGIN 
  OPEN cur(c1=>'01');
  FETCH cur INTO rec;
  DBMS_OUTPUT.PUT_LINE(rec.c1 ||' '||rec.c2);
  CLOSE cur;
END;
/
--result
01 EastChina

-- Cursor for loop
DECLARE
CURSOR emp_cur IS SELECT * FROM employees;
BEGIN
    DBMS_OUTPUT.PUT_LINE('EMPNO    ENAME');
    DBMS_OUTPUT.PUT_LINE('-----    -------');
    FOR v_emp_rec IN emp_cur LOOP
        DBMS_OUTPUT.PUT_LINE(v_emp_rec.employee_no || '     ' || v_emp_rec.employee_name);
    END LOOP;
END;
/

--result
EMPNO    ENAME
-----    -------
0101000001     Mask
0101000002     John
0201010011     Anna
0201008003     Jack
0201008004     Jim

-- Single table SELECT … FOR UPDATE
SELECT product_no,cost,price FROM product;

PRODUCT_NO        COST       PRICE
---------- ----------- -----------
11001                8          10
11002               13          16
10001               99         100
10002              199         200

DECLARE
    CURSOR cursor_for_up IS
    SELECT product_no,cost,price FROM product FOR UPDATE;
    t_rec1 cursor_for_up%ROWTYPE;
BEGIN
    DBMS_OUTPUT.PUT_LINE('PRODUCT_NO    COST');
    DBMS_OUTPUT.PUT_LINE('----------    -----');
    OPEN cursor_for_up;
    LOOP
    FETCH cursor_for_up INTO t_rec1;
        EXIT WHEN cursor_for_up%notfound;
        UPDATE product SET cost = ROUND(cost * 0.8) WHERE CURRENT OF cursor_for_up;
        t_rec1.cost := ROUND(t_rec1.cost * 0.8);
        DBMS_OUTPUT.PUT_LINE(RPAD(t_rec1.product_no, 10) || '    ' || t_rec1.cost);
    END LOOP;
    COMMIT;
    CLOSE cursor_for_up;
END;
/
--result
PRODUCT_NO    COST
----------    -----
11001         6
11002         10
10001         79
10002         159

-- Multi-table join SELECT … FOR UPDATE
DECLARE
    CURSOR cursor_update_join IS
    SELECT * FROM product p,sales_info s WHERE p.product_no = s.product AND p.product_no = '11001' FOR UPDATE OF p.price ORDER BY p.product_no;  -- This will only lock the corresponding rows in the product table
    t_rec2 cursor_update_join%rowtype;
BEGIN
    OPEN cursor_update_join;
    LOOP
        FETCH cursor_update_join INTO t_rec2;
        EXIT WHEN cursor_update_join%notfound;
        IF t_rec2.price < 120 then
            UPDATE product SET price = ROUND(price * 1.2) WHERE CURRENT OF cursor_update_join;
        ELSE 
            DELETE FROM product WHERE CURRENT OF cursor_update_join;
        END IF;
    END LOOP;    
    CLOSE cursor_update_join;
    COMMIT;
END;
/

SELECT product_no,cost,price FROM product;

PRODUCT_NO        COST       PRICE
---------- ----------- -----------
11001                6          29
11002               10          16
10001               79         100
10002              159         200
```

## Dynamic Cursors

Dynamic cursors require first defining a reference cursor type and then declaring a cursor variable of this type. After that, operations such as [opening a cursor](../../PL Statements/OPEN Statement), [fetching from a cursor](../../PL Statements/FETCH Statement), and [closing a cursor](../../PL Statements/CLOSE Statement) can be executed in the procedure body.

**ref_cursor_type_definition::=**

```ebnf
= TYPE type IS REF CURSOR [RETURN ((table_or_view|cursor|cursor_variable) "%ROWTYPE"|
				record "%TYPE"|
				record_type)] ";".
```

**cursor_variable_declaration::=**

```ebnf
= cursor_variable type [[NOT] NULL]";".
```

**RETURN clause**

The RETURN clause explicitly defines the return value held by the dynamic cursor. This return value must be recorded through a RECORD type, including:

- %ROWTYPE refers to a table, view, explicit cursor, or cursor variable (must be an explicitly defined cursor variable that has a return value).
- %TYPE references a previously defined RECORD type variable.
- A previously defined RECORD type variable.

If the query columns of the SQL statement being opened cannot be implicitly converted to the members recorded by the RECORD, the SQL statement cannot be assigned to the cursor variable.

This statement can be omitted; in this case, a cursor variable without a specified result set is declared, which allows binding to different SQL statements and returning different RECORD types.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Dynamic cursor declaration without explicitly defined return value
DECLARE
  TYPE cursor IS REF CURSOR;
  cur cursor;
  TYPE record1 IS RECORD (
    c1 CHAR(2),
	c2 VARCHAR(20)
  );
  rec1 record1;
  TYPE record2 IS RECORD (
    c1 CHAR(2),
	c2 VARCHAR(20),
	c3 INT
  );
  rec2 record2;
BEGIN 
  OPEN cur FOR SELECT area_no, area_name FROM area WHERE area_no='01';
  FETCH cur INTO rec1;
  DBMS_OUTPUT.PUT_LINE(rec1.c1 ||' '||rec1.c2);
  CLOSE cur;
  OPEN cur FOR SELECT area_no, area_name, LENGTH(area_name) FROM area WHERE area_no='01';
  FETCH cur INTO rec2;
  DBMS_OUTPUT.PUT_LINE(rec2.c1 ||' '||rec2.c2||' '||rec2.c3);
  CLOSE cur;
END;
/
--result
01 EastChina
01 EastChina 2

-- Dynamic cursor declaration with explicitly defined return value
DECLARE
  TYPE cursor1 IS REF CURSOR RETURN area%ROWTYPE;
  cur1 cursor1;
  TYPE cursor2 IS REF CURSOR RETURN cur1%ROWTYPE;
  cur2 cursor2;
  TYPE record IS RECORD (
    c1 CHAR(2),
	c2 VARCHAR(20),
	c3 VARCHAR(20)
  );
  rec record;
BEGIN  
  OPEN cur2 FOR SELECT * FROM area WHERE area_no='01';
  FETCH cur2 INTO rec;
  DBMS_OUTPUT.PUT_LINE(rec.c1 ||' '||rec.c2||' '||rec.c3);
  CLOSE cur2;
END;
/
--result
01 EastChina Shanghai
```

## sys_refcursor

The sys_refcursor is a system predefined dynamic cursor that does not specify return values and can be directly used in cursor variable declarations. Its usage is consistent with dynamic cursors.

**Cursor parameters**

Based on the system predefined sys_refcursor, cursors can be used as [PL parameters](../../Parameters/00Parameters):

- As an IN parameter for stored procedures or UDFs.
- As an OUT parameter for stored procedures.
- As a return value for UDFs.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
CREATE OR REPLACE PROCEDURE getArea(cur IN OUT sys_refcursor) AS
BEGIN
  OPEN cur FOR SELECT * FROM area WHERE area_no='01';
END;
/

CREATE OR REPLACE FUNCTION getArea_func RETURN sys_refcursor AS
  cur sys_refcursor;
BEGIN
  OPEN cur FOR SELECT * FROM area WHERE area_no='02';
  RETURN cur;
END;
/

DECLARE
  TYPE record IS RECORD (
    c1 CHAR(2),
	c2 VARCHAR(20),
	c3 VARCHAR(20)
  );
  rec record;
  cur sys_refcursor;
BEGIN
  cur := getArea_func;
  FETCH cur INTO rec;
  DBMS_OUTPUT.PUT_LINE(rec.c1 ||' '||rec.c2||' '||rec.c3);
  CLOSE cur;
  getArea(cur);
  FETCH cur INTO rec;
  DBMS_OUTPUT.PUT_LINE(rec.c1 ||' '||rec.c2||' '||rec.c3);
  CLOSE cur;
END;
/

--result 
02 WestChina Chengdu
01 EastChina Shanghai
```

Cursor Assignment
-------

YashanDB supports directly assigning one cursor variable to another, with the following rules:

- Explicit cursors cannot be used as the left-hand side or right-hand side of any assignment expression.
- Two dynamic cursors with the same return column items can assign values to each other.
- Cursors without return values can be assigned to any non-explicit cursor on the left-hand side.
- Cursors with return values will check the return column items of the right-hand side cursor when acting as the left-hand side; if the right-hand side cursor is unbound SQL statement, or its return column items match those of the left-hand cursor, assignment is allowed.
- For IN and IN OUT cursor actual parameters in stored procedures/functions, assignment rules will be checked at the start of execution, and checks for OUT and IN OUT cursor actual parameters will occur before returning from the execution.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
DECLARE
  cur0 sys_refcursor;
  TYPE cursor IS REF CURSOR RETURN area%ROWTYPE;
  cur cursor;
  TYPE record IS RECORD (
    c1 CHAR(2),
	c2 VARCHAR(20),
	c3 VARCHAR(20)
  );
  rec record;
BEGIN 
  OPEN cur FOR SELECT * FROM area WHERE area_no='01';
  cur0 := cur;
  FETCH cur0 INTO rec;
  DBMS_OUTPUT.PUT_LINE(rec.c1 ||' '||rec.c2||' '||rec.c3);
  CLOSE cur;
END;
/
--result
01 EastChina Shanghai
```

## Cursor Attributes

Cursors have four attributes as follows:

|Attribute |Return Type |Purpose |
| :-------- | :--------- | :----------------------- |
| %isopen     | Boolean     | Determine if the cursor is open       |
| %found      | Boolean     | Determine if the cursor has retrieved a value     |
| %notfound   | Boolean     | Determine if the cursor has not retrieved a value |
| %rowcount   | BIGINT      | Number of rows successfully processed currently  |

These attributes can only be used in procedural statements, not in SQL statements. The usage format is as follows:

*cursor_name%isopen, cursor_name%found, cursor_name%notfound, cursor_name%rowcount*

Where `cursor_name` is the name of the cursor, examples of usage can be found in [FETCH Statement](../../PL Statements/FETCH Statement).

<span id="hidecursor" name="hidecursor"></span>

**Implicit Cursor Attributes**

When executing an SQL statement in the procedure body, the system generates an implicit cursor for it. Implicit cursors do not need to be declared and managed by users, but users can operate on their result sets using the above cursor attributes, with the usage format as follows:

*SQL%isopen, SQL%found, SQL%notfound, SQL%rowcount*

***Example***

```plsql
BEGIN
  UPDATE area SET area_name='cursor example';
  IF SQL%found THEN
    DBMS_OUTPUT.PUT_LINE('Total '||SQL%rowcount||' lines are deleted.');
  END IF;
  ROLLBACK;
END;
/

--result
Total 5 lines are deleted.
```
