The WHILE Statement is a loop control statement, and its syntax is as follows:

_while condition loop_

_......_

_end loop \[label_name\];_

Where condition is an expression that yields a Boolean result.

label_name is optional and serves as a label to identify the loop body, improving readability. If label_name is used, there must be a matching label definition at the start of the loop body, otherwise an error will be raised.

The WHILE Statement first evaluates the result of condition. If it is true, it enters the loop statements, and after completing them, it evaluates condition again. The loop continues until the result of condition is false, therefore:

* Before the WHILE Statement, it should be ensured that the result of condition is true; otherwise, the loop statements cannot be executed.
* Inside the loop statements, there must be a statement that causes condition to yield false, or there should be control statements such as [GOTO Statement](GOTO Statement), [EXIT Statement](EXIT Statement), [CONTINUE Statement](CONTINUE Statement), otherwise it will lead to an infinite loop.

***Example***

```plsql
DECLARE
    i INT;
BEGIN
    i := 0;
    WHILE i < 5 LOOP
        i := i + 1;
        IF i=3 THEN
            GOTO a;
        END IF;
        DBMS_OUTPUT.PUT_LINE ('This is: '||i);
    END LOOP;
    <<a>>
    DBMS_OUTPUT.PUT_LINE ('The End');
END;
/
 
--result
This is: 1
This is: 2
The End

CREATE OR REPLACE PROCEDURE ya_proc(b INT) IS
    a INT := 1;
BEGIN
    WHILE a<=b LOOP
        DBMS_OUTPUT.PUT_LINE ('while:' || a);
        a := a+1;
    END LOOP;
END;
/

exec ya_proc(3);

--result
while:1
while:2
while:3
```
