The GOTO Statement is a control statement, and its syntax is as follows:

_<<label\_name>> statement_

_..._

_GOTO label\_name;_

Here, <<>> is used to define a label for the following statement, and label_name is the name of the defined label.

<<label\_name>>
---------------

This is used to define a label for the following statement, and label_name is the name of the defined label.

In the same PL procedure body, label names cannot be duplicated, otherwise an error will be raised.

Labels can be located anywhere in the procedure body, except in the following locations:

*   Before END IF
*   Before END LOOP
*   Before END CASE
*   Before END
*   Before WHEN in a CASE Statement
*   Before EXCEPTION
*   Before the procedure body ending character '/'
*   After the `END` of an anonymous block

When defining a label for the following statements:

*   A program block starting with DECLARE, at this point, label_name can be used not only to indicate the jump position but also as the name of the program block, for example, accessing declared variables in this program block through label_name.variable_name.
*   A loop statement starting with FOR, at this point, label_name can also be used as the name of this FOR statement, for example, accessing declared variables in this FOR statement through label_name.variable_name.

GOTO label\_name
----------------

GOTO label_name indicates jumping to the statement pointed to by label_name, and execution will begin from this statement.

When GOTO label_name is inside a clause of a PL statement, it can only jump outward to an outer level and cannot jump to the same level or a deeper level.

***Example***

```plsql
--label_name is not sequentially used with a program block that starts with DECLARE
CREATE OR REPLACE PROCEDURE ya_proc(a varchar, b varchar) as
BEGIN
    <<label1>>
    DBMS_OUTPUT.PUT_LINE(a);
    <<label2>>
        DECLARE
        a INT:=3;
        b INT:=3;
        BEGIN
            select * into label1.a from dual;
            select * into label2.b from dual;
        DBMS_OUTPUT.PUT_LINE('a+1 = '|| a);
        DBMS_OUTPUT.PUT_LINE('b+1 = '|| b);
        END;
    END;
/
YAS-04253 PL/SQL compiling errors:
[10:27] YAS-04243 invalid identifier "LABEL1"."A"

--label_name is sequentially used with a program block that starts with DECLARE
CREATE OR REPLACE PROCEDURE ya_proc(a varchar, b varchar) as
BEGIN
    <<label1>>
    <<label2>>
        DECLARE
        a INT:=3;
        b INT:=3;
        BEGIN
            select * into label1.a from dual;
            select * into label2.b from dual;
        DBMS_OUTPUT.PUT_LINE('a+1 = '|| a);
        DBMS_OUTPUT.PUT_LINE('b+1 = '|| b);
        END;
    END;
/
Succeed.

CREATE OR REPLACE PROCEDURE ya_proc() IS
BEGIN
    CASE WHEN true THEN
    <<a>>
        DBMS_OUTPUT.PUT_LINE('hello');
    WHEN false THEN
        DBMS_OUTPUT.PUT_LINE('world');
    END CASE;
    GOTO a;
END;
/
YAS-04253 PL/SQL compiling errors:
[9:1] YAS-05223 invalid goto statement cannot jump to to label A

CREATE OR REPLACE PROCEDURE ya_proc() IS
BEGIN
    FOR i IN 1..3 LOOP
        DBMS_OUTPUT.PUT_LINE(i);
        GOTO a;
    END LOOP;
    <<a>>
    CASE WHEN true THEN
        DBMS_OUTPUT.PUT_LINE('hello');
    WHEN false THEN
        DBMS_OUTPUT.PUT_LINE('world');
    END CASE;
END;
/
  
exec ya_proc; 
  
--result
1
hello
```
