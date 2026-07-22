The RETURN Statement is a control statement that ends the execution of the current procedure immediately and returns the Statement content to the caller.

RETURN Statement in function
----------------------------

The syntax for using the RETURN Statement in a UDF is:

_RETURN v\_result;_

Where v\_result can be a variable, constant, or expression.

This statement cannot be omitted, meaning that every execution path in the procedure (branches of control statements, branches of exception handling units) must include at least one RETURN Statement, and v\_result cannot be omitted, but the value of v\_result can be NULL.

***Example***

```plsql
CREATE OR REPLACE FUNCTION ya_func RETURN INT IS
a VARCHAR(10) := '100';
BEGIN
    DBMS_OUTPUT.PUT_LINE('ya_func');
    RETURN TO_NUMBER(a)+10;
END;
/
 
SELECT TO_CHAR(ya_func) FROM dual;
 
--result
 
ya_func
TO_CHAR(YA_FUNC)
-------------------
110

-- EXCEPTION Handling
CREATE OR REPLACE FUNCTION ya_func (name_in in varchar) RETURN DATE IS
entry_out DATE;
BEGIN
    SELECT entry_date INTO entry_out FROM employees WHERE department NOT IN (SELECT deparment_no FROM department);
    RETURN(entry_out);
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            DBMS_OUTPUT.PUT_LINE ('NO_DATA_FOUND');
        WHEN OTHERS THEN
            DBMS_OUTPUT.PUT_LINE ('Unexpected error');
END;
/

--result
SELECT ya_func('jack') FROM dual;
NO_DATA_FOUND

[11:1]YAS-05228 function must has return sql
```

RETURN Statement in others
----------------------------

The syntax for using the RETURN Statement in stored procedures, anonymous blocks, package (body), and triggers is:

_RETURN ;_

Where return cannot be followed by a variable, constant, or expression.

This statement can be omitted; when used, it indicates that subsequent statements will not be executed and ends the program.

***Example***

```plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
A VARCHAR(10) := '100';
BEGIN
    DBMS_OUTPUT.PUT_LINE('ya_proc 1');
    RETURN;
    DBMS_OUTPUT.PUT_LINE('ya_proc 2');
END;
/

--result
CALL ya_proc();
ya_proc 1
```
