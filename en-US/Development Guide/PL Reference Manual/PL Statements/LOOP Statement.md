The LOOP Statement is a control statement for loops, and its syntax is as follows:

_loop_

_......_

_end loop \[label\_name\];_

Here, label_name is optional and serves as a label to identify the loop body, enhancing readability. If label_name is used, there must be a matching label definition at the beginning of the loop body; otherwise, an error will occur.

The LOOP Statement unconditionally enters the loop and returns to the top of the loop for execution again after finishing, unless there are control statements or exceptions that cause it to exit the loop. Therefore, the loop must contain sequential control statements such as [GOTO Statement](GOTO Statement), [EXIT Statement](EXIT Statement), or [CONTINUE Statement](CONTINUE Statement); otherwise, it will result in an infinite loop.

***Example***

```plsql
DECLARE
i int;
BEGIN
    i := 0;
    <<loop1>>
    LOOP
        i := i + 1;
        IF i=3 THEN
            GOTO a;
        END IF;
        DBMS_OUTPUT.PUT_LINE ('This is: '||i);
    END LOOP loop1;
    <<a>>
    DBMS_OUTPUT.PUT_LINE ('The End');
END;
/
 
--result
This is: 1
This is: 2
The End
```

***Example***

```plsql
DECLARE
a int :=1;
BEGIN
    LOOP
        DBMS_OUTPUT.PUT_LINE('loop:'||a);
        a := a+1;
        EXIT WHEN a > 2;
    END LOOP;
END;
/

--result
loop:1
loop:2
```
