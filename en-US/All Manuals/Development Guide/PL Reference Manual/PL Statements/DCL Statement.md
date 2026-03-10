DCL Statement is a static SQL execution statement.

COMMIT|ROLLBACK
---------------

The syntax for COMMIT/ROLLBACK can be referenced in the corresponding SQL statements ([COMMIT](../../SQL Reference Manual/SQL Statements/COMMIT) and [ROLLBACK](../../SQL Reference Manual/SQL Statements/ROLLBACK)), but the FORCE clause cannot be used in PL statements.

In PL loop control statements, COMMIT is equivalent to COMMIT WRITE BATCH NOWAIT.

***Example***

```plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
no VARCHAR(10);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
BEGIN
  
  
EXECUTE IMMEDIATE str1 INTO no,name USING '03';       -- dynamic SQL
DBMS_OUTPUT.PUT_LINE('Area Number:'||no||'Area Name:'||name);
  
UPDATE area SET area_no='00' WHERE area_no='03';    -- static SQL
COMMIT;        -- static SQL
  
EXECUTE IMMEDIATE str1 INTO no,name USING '03' ;       -- dynamic SQL, since the UPDATE statement was executed, this statement will RETURN no results
DBMS_OUTPUT.PUT_LINE('Area Number:'||no||'Area Name:'||name);
END;
/
   
exec ya_proc;
   
--result
Area Number:03Area Name:South China
[9:1]YAS-05206 no data found
```

SAVEPOINT
---------

The syntax for SAVEPOINT is as follows:

_BEGIN_  
_SAVEPOINT _savepoint_name_;_  
_..._  
_ROLLBACK TO _savepoint_name_;_  
_..._  
_END;_

***Example***

```plsql
-- The program captures a ZERO_DIVIDE exception. When executing rollback, the previously inserted data into the area table is rolled back, so the second execution of str1 statement throws a no data found exception.
DECLARE
a INT := 0;
b INT := 1;
c INT;
no VARCHAR(10);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=''99''';
BEGIN
SAVEPOINT start_trans;
c := 2;
INSERT INTO area VALUES('99','other',DEFAULT);
EXECUTE IMMEDIATE str1 INTO no,name ;
DBMS_OUTPUT.PUT_LINE('Area Number:'||no||'Area Name:'||name);
c := b/a;
EXCEPTION
WHEN ZERO_DIVIDE THEN
ROLLBACK TO start_trans;
EXECUTE IMMEDIATE str1 INTO no,name ;
DBMS_OUTPUT.PUT_LINE('Area Number:'||no||'Area Name:'||name);
END;
/
 
--result
Area Number:99Area Name:other
[18:1]YAS-05206 no data found
```
