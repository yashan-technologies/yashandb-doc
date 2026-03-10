The OPEN statement is used to open a [cursor](../../All Manuals/Development Guide/PL Reference Manual/PL Language Fundamentals/Variables/Cursors). Opening a cursor means executing the SQL statement to which the cursor is bound (either static binding or dynamic binding) and obtaining the result set.

Opening an Explicit Cursor
------------

The syntax format for opening an explicit cursor is:

_OPEN cursor[cursor_parameters];_

Where cursor is the name of the explicit cursor, and cursor cannot be a binding parameter. cursor_parameters are the input values for the explicit cursor parameters.

If the explicit cursor has a default value defined for a parameter during the declaration phase, the corresponding cursor_parameters can be omitted here; otherwise, each declared parameter must have a corresponding input value in this statement.

The cursor_parameters from the declaration phase correspond one-to-one with the cursor_parameters during the opening phase in order unless the parameter passing order is specified using "=>".

Once an explicit cursor is opened, it will retain the corresponding result set, and reopening it is not allowed until it is closed.

***Example***

```plsql
DECLARE 
  CURSOR cur(areano CHAR DEFAULT '02', branchno CHAR, orderdate DATE DEFAULT SYSDATE) IS
  SELECT area, branch, order_no FROM orders_info 
  WHERE area = areano 
  AND branch = branchno 
  AND order_date < orderdate;
  TYPE record1 IS RECORD (
    c1 CHAR(2),
	c2 CHAR(4),
	c3 VARCHAR(20)
  );
  rec record1;
BEGIN 
  DBMS_OUTPUT.PUT_LINE('First open:');
  OPEN cur(branchno =>'0201');
  FETCH cur INTO rec;
  DBMS_OUTPUT.PUT_LINE(rec.c1 ||' ,'||rec.c2 ||' ,'||rec.c3);
  CLOSE cur;
  DBMS_OUTPUT.PUT_LINE('Second open:');
  OPEN cur('02','0201');
  FETCH cur INTO rec;
  DBMS_OUTPUT.PUT_LINE(rec.c1 ||' ,'||rec.c2 ||' ,'||rec.c3);
  CLOSE cur;
END;
/

--result
First open:
02 ,0201 ,20010102020001
Second open:
02 ,0201 ,20010102020001
```

Opening a Dynamic Cursor
-------

The syntax format for opening a dynamic cursor is:

_OPEN cursor_variable FOR select_statement;_

Where cursor_variable is the name of the cursor variable; select_statement is the SQL statement to which the cursor will be bound. This SQL statement can either be a static SQL statement or a dynamic SQL statement carried by a variable. When using a static SQL statement, the INTO clause can be used for syntactical compatibility with no actual effect. When using a dynamic SQL statement, the USING clause can be used to include [binding parameters](../../All Manuals/Development Guide/PL Reference Manual/Parameters/Binding Parameters). The syntax format then becomes:

_OPEN cursor_variable FOR string_variable USING bind_variable_list;_

A dynamic cursor can be opened multiple times (it can bind different SQL statements, and the return value types can also differ) and does not require an explicit close cursor statement. However, when the cursor is no longer used, it should be explicitly closed.

Static SQL Statement:

***Example***

```plsql
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
  OPEN cur FOR SELECT area_no, area_name, LENGTH(area_name) FROM area WHERE area_no='02';
  FETCH cur INTO rec2;
  DBMS_OUTPUT.PUT_LINE(rec2.c1 ||' '||rec2.c2||' '||rec2.c3);
  CLOSE cur;
END;
/

--result
01 EastChina
02 WestChina 2
```

Dynamic SQL Statement:

***Example***
```plsql
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
  sql1 CHAR(200) := 'SELECT area_no, area_name FROM area WHERE area_no = :1';
  sql2 CHAR(200) := 'SELECT area_no, area_name, LENGTH(area_name) FROM area WHERE area_no= :1';
  bind1 CHAR(5) := '01';
  bind2 CHAR(5) := '02';
BEGIN 
  OPEN cur FOR sql1 USING bind1;
  FETCH cur INTO rec1;
  DBMS_OUTPUT.PUT_LINE(rec1.c1 ||' '||rec1.c2);
  OPEN cur FOR sql2 USING bind2;
  FETCH cur INTO rec2;
  DBMS_OUTPUT.PUT_LINE(rec2.c1 ||' '||rec2.c2||' '||rec2.c3);
  CLOSE cur;
END;
/

--result
01 EastChina
02 WestChina 2
```

The IS [NOT] NULL operator can be used to determine whether a dynamic cursor has bound an SQL statement:

_cursor_variable IS [NOT] NULL_

Where cursor_variable is the name of the cursor variable.

- IS NULL：

  - If the dynamic cursor has not been bound to an SQL statement, the result is TRUE.

  - If the dynamic cursor has been bound to an SQL statement, the result is FALSE.


- IS NOT NULL：

  - If the dynamic cursor has been bound to an SQL statement, the result is TRUE.

  - If the dynamic cursor has not been bound to an SQL statement, the result is FALSE.

```plsql
DECLARE
c SYS_REFCURSOR;
BEGIN
IF c IS null THEN
    dbms_output.put_line('BEFORE OPEN NULL');
ELSIF c IS NOT null THEN
    dbms_output.put_line('BEFORE OPEN NOT NULL');
END IF;

-- OPEN
OPEN c FOR SELECT * FROM dual;

IF c IS null THEN
    dbms_output.put_line('AFTER OPEN NULL');
ELSIF c IS NOT null THEN
    dbms_output.put_line('AFTER OPEN NOT NULL');
END IF;

-- CLOSE
CLOSE c;

IF c IS null THEN
    dbms_output.put_line('AFTER CLOSE NULL');
ELSIF c IS NOT null THEN
    dbms_output.put_line('AFTER CLOSE NOT NULL');
END IF;

dbms_output.put_line('END');
END;
/

--result
BEFORE OPEN NULL
AFTER OPEN NOT NULL
AFTER CLOSE NOT NULL
END
```