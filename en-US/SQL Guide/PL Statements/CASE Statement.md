The CASE statement is a conditional selection control statement, which includes the following two forms:

*   CASE selector WHEN
*   CASE WHEN condition

CASE selector WHEN
------------------

The format is:

_CASE selector_  
_WHEN selector_value_1 THEN statements_1_  
_WHEN selector_value_2 THEN statements_2_  
_..._  
_WHEN selector_value_n THEN statements_n_  
_\[ ELSE __else_statements \]_  
_END CASE;_

Among them, selector, selector_value_1, selector_value_2, ..., selector_value_n are in the form of expressions, and selector cannot be of boolean type.

Meaning:

First, compute the value of the selector in the CASE statement. If the value is NULL, skip all WHEN clauses and proceed to search for an ELSE clause. If an ELSE clause is found, execute else_statements; otherwise, an error is reported.


If the value of selector is not NULL, compare it with the value of selector_value_1 in the first subsequent WHEN clause:

*   If the value of selector equals selector_value_1, execute statements_1 following THEN in the first WHEN clause, then jump to END CASE.

*   If the value of selector does not equal selector_value_1, jump to the second WHEN clause and perform the same operation as with the first WHEN clause.

* ......

*   If the value of selector equals selector_value_n, execute statements_n following THEN in the nth WHEN clause, then jump to END CASE.

*   If the value of selector does not equal selector_value_n, jump to the next statement:

If the next statement is an ELSE clause, execute else_statements, then jump to END CASE.

If the next statement is not an ELSE clause, throw an error.

***Example***

```plsql
CREATE OR REPLACE PROCEDURE ya_proc() IS
i INT:=0;
BEGIN
    CASE i
    WHEN 1 THEN
        DBMS_OUTPUT.PUT_LINE('hello');
    WHEN 2 THEN
        DBMS_OUTPUT.PUT_LINE('world');
    END CASE;
END ya_proc;
/
 
exec ya_proc;
 
--result
YAS-05210 case not found

-- Includes an ELSE clause
CREATE OR REPLACE PROCEDURE ya_proc(a INT) IS
BEGIN
    CASE a
    WHEN 2 THEN
        DBMS_OUTPUT.PUT_LINE('loop2');
    WHEN 3 THEN
        DBMS_OUTPUT.PUT_LINE('loop3');
    ELSE
        DBMS_OUTPUT.PUT_LINE('loop');
    END CASE;
END;
/

exec ya_proc(3);

--result
loop3
```

CASE WHEN condition
-------------------

The format is:

_CASE_  
_WHEN condition_1 THEN statements_1_  
_WHEN condition_2 THEN statements_2_  
_..._  
_WHEN condition_n THEN statements_n_  
_\[ ELSE __else_statements \]_  
_END CASE;_

Among them, condition_1, condition_2, ..., condition_n are conditional expressions that evaluate to boolean results.

Meaning:

First, evaluate the condition condition_1 in the first WHEN clause:

*   If condition_1 evaluates to true, execute statemnets_1 following THEN in the first WHEN clause, then jump to END CASE.

*   If condition_1 evaluates to false, jump to the second WHEN clause and perform the same operation as with the first WHEN clause.

*   ......

*   If condition_n evaluates to true, execute statemnets_n following THEN in the nth WHEN clause, then jump to END CASE.
*   If condition_n evaluates to false, jump to the next statement.

If the next statement is an ELSE clause, execute else_statements, then jump to END CASE.

If the next statement is not an ELSE clause, throw an error.

***Example***

```plsql
CREATE OR REPLACE PROCEDURE ya_proc() IS
BEGIN
    CASE WHEN true THEN
        DBMS_OUTPUT.PUT_LINE('hello');
    WHEN false THEN
        DBMS_OUTPUT.PUT_LINE('world');
    END CASE;
END;
/
 
exec ya_proc;
 
--result
hello
```
