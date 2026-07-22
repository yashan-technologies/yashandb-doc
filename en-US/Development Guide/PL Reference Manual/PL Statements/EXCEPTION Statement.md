The EXCEPTION Statement is an exception handling statement, also known as an exception handler. Its syntax is as follows:

*EXCEPTION*  

*WHEN exception\_name1 THEN  
statements*

*WHEN exception\_name2 THEN*
*statements*

*...*

*WHEN OTHERS THEN* 

*statements;*

In this syntax, the exception_name following WHEN can be connected by OR for multiple exceptions, but OTHERS cannot be connected with other exception_names using OR.

Within a program block 'BEGIN END;', this statement can only appear once. By nesting program blocks, multiple layers of EXCEPTION Statements can be achieved, allowing the outer layer to catch exceptions thrown during the execution of the inner layer. For exceptions not caught in the inner layer, the outer layer can continue to catch them until the exception is successfully caught, or an error is reported if it remains uncaught at the outermost layer.

The error codes corresponding to exception_names at the same level cannot be duplicated, while different levels can share the same error codes.

exception\_name
---------------

An already defined exception name used for consistent identification in the process body for throwing exceptions and capturing exceptions in the exception handler.

The exception name may be a system-defined exception name or a user-defined exception name. For specific descriptions, see [Exception Handling](../PL Exception Handling) documentation.

exception handler
-----------------

YashanDB's rules for exception handling in the process body are as follows:

* If an error occurs during execution and the error code matches an exception_name following a WHEN in the EXCEPTION Statement, the corresponding statements after THEN are executed.
* If an error occurs during execution and the error code does not match any exception_name following a WHEN in the EXCEPTION Statement, it will match OTHERS and execute the corresponding statements after THEN.
* If an error occurs during execution and the error code does not match any exception_name following a WHEN in the EXCEPTION Statement in the current and outer process bodies, it is thrown as an unhandled exception.
* If an error occurs during execution and an exception is caught, any new error occurring during exception handling will overwrite the previously caught error.

***Example***

```plsql
CREATE OR REPLACE PROCEDURE ya_proc(vno IN OUT VARCHAR) IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
BEGIN
DBMS_OUTPUT.PUT_LINE(vno);
EXECUTE IMMEDIATE str1 INTO no,name USING vno;      
DBMS_OUTPUT.PUT_LINE('num:'||no||'name:'||name);
IF vno='01' THEN
vno := TO_NUMBER(vno)/0;
END IF;
IF vno='02' THEN
update area set area_no='00' where area_no=vno;             -- Due to the foreign key constraint value existing in the child table, this statement will cause a foreign key constraint error
END IF;
EXCEPTION
WHEN NO_DATA_FOUND THEN
DBMS_OUTPUT.PUT_LINE('no data found');
WHEN ZERO_DIVIDE THEN
BEGIN
DBMS_OUTPUT.PUT_LINE('first:0 divide');
vno := TO_NUMBER(vno)/0;
EXCEPTION
WHEN NO_DATA_FOUND OR ZERO_DIVIDE THEN
DBMS_OUTPUT.PUT_LINE('second:0 divide');
END;
WHEN OTHERS THEN
DBMS_OUTPUT.PUT_LINE('unknown error');
END;
/
   
DECLARE
 a VARCHAR(2);
BEGIN
DBMS_OUTPUT.PUT_LINE('--------------------------');
a := '10';
ya_proc(a);                        --NO_DATA_FOUND
DBMS_OUTPUT.PUT_LINE('--------------------------');
a := '01';
ya_proc(a);                        --ZERO_DIVIDE
DBMS_OUTPUT.PUT_LINE('--------------------------');
a := '02';
ya_proc(a);                        --OTHERS
END; 
/
--result
--------------------------
10
no data found
--------------------------
01
num:01name:EastChina
first:0 divide
second:0 divide
--------------------------
02
num:02name:westofchina
unknown error
```
