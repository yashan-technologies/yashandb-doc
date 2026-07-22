The EXIT Statement is a control flow statement. Its syntax is as follows:

_EXIT \[label\_name\] \[WHEN condition\];_

Where, label\_name is optional and identifies the current or outer loop to exit; WHEN condition is optional, with condition being an expression that produces a boolean result.

The EXIT statement is used to exit a loop, jumping to the end of the loop and continuing execution downward. Note the following:

* label\_name indicates the current or outer loop to exit. If the label\_name does not correspond to the current or outer loop, or if it does not exist or is in other illegal situations, an error will be raised. label\_name can be omitted, in which case the EXIT statement will exit the current loop when executed.

* The result of the condition in the WHEN condition clause determines whether the EXIT statement is executed. If true, it will execute; if false, it will not execute. If WHEN condition is omitted, the EXIT statement will execute by default.
* The EXIT statement can only appear within loops, including [WHILE Statement](WHILE Statement), [FOR Statement](FOR Statement), and [LOOP Statement](LOOP Statement).

***Example***

```plsql
DECLARE
i int;
BEGIN
i := 0;
<<loop1>>
LOOP
i := i + 1;
EXIT loop1 WHEN i = 3;
DBMS_OUTPUT.PUT_LINE ('This is: '||i);
END LOOP loop1;
DBMS_OUTPUT.PUT_LINE ('The End');
END;
/
 
--result
This is: 1
This is: 2
The End
```
