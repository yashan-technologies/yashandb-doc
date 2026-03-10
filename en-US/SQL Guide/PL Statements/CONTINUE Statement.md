The CONTINUE statement is a control flow statement, and its syntax is as follows:

_CONTINUE \[label_name\] \[WHEN condition\];_

Here, label_name is optional and identifies the current or outer loop that needs to skip the iteration; WHEN condition is optional, and condition is an expression that generates a boolean result.

The CONTINUE statement is used to skip the current iteration of the loop and jump to the beginning of the loop to execute the next iteration. Note that:

* label_name identifies the current or outer loop to skip the iteration. If label_name corresponds to a label that does not identify the current or outer loop, or if it is nonexistent or otherwise invalid, an error will occur. label_name can be omitted, in which case when executing the CONTINUE statement, the current iteration of the current loop will be skipped.

* The condition result in the WHEN condition clause is used to determine whether to execute the CONTINUE statement, with true resulting in execution and false resulting in non-execution. When the WHEN condition clause is omitted, the CONTINUE statement will be executed by default.

***Example***

```plsql
DECLARE
i INT;
BEGIN
    i := 0;
    <<loop1>>
    WHILE i < 3 LOOP
        i := i + 1;
        CONTINUE loop1 WHEN i = 2;
        DBMS_OUTPUT.PUT_LINE ('This is: '||i);
    END LOOP loop1;
    DBMS_OUTPUT.PUT_LINE ('The End');
END;
/
 
--result
This is: 1
This is: 3
The End

DECLARE
    a INT;
BEGIN
    a := 1;
    LOOP
        a :=a + 1;
        DBMS_OUTPUT.PUT_LINE('loop1');
        CONTINUE WHEN a < 5;
        DBMS_OUTPUT.PUT_LINE('loop2');
        EXIT;
    END LOOP;
END;
/

--result
loop1
loop1
loop1
loop1
loop2
```
