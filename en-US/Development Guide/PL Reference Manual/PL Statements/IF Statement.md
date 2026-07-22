The IF Statement is a conditional selection control statement, which includes the following three forms:

*   IF THEN
*   IF THEN ELSE 
*   IF THEN ELSIF

IF THEN
-------

The format is:

_IF condition THEN statements END IF;_

Where condition is an expression that can produce a Boolean value.

The meaning is as follows:

Evaluate the condition in the IF statement:

*   If the value of condition is true, execute the statements following THEN, then continue execution until END IF is reached;
*   If the value of condition is false, do nothing and jump to END IF.

***Example***

```plsql
DECLARE
i int;
BEGIN
    i := 0;
    IF i < 5 THEN
        i := i + 1;
        DBMS_OUTPUT.PUT_LINE ('This is: '||i);
    END IF;
END;
/
 
--result
This is: 1
```

IF THEN ELSE
------------

The format is:

_IF condition THEN statements_  
_ELSE statements_  
_END IF;_

Where condition is an expression that can produce a Boolean value.

The meaning is as follows:

First, evaluate the condition in the IF statement:

*   If the value of condition is true, execute the statements following THEN in the IF statement, then jump to END IF;
*   If the value of condition is false, jump to the ELSE statement, execute the statements in the ELSE statement, then continue execution until END IF is reached.

***Example***

```plsql
DECLARE
i int;
BEGIN
    i := 5;
    IF i < 5 THEN
        i := i + 1;
        DBMS_OUTPUT.PUT_LINE ('This is: '||i);
    ELSE
        DBMS_OUTPUT.PUT_LINE ('This is: '|| (i-5));
    END IF;
END;
/
 
--result
This is: 0
```

IF THEN ELSIF
-------------

The format is:

_IF condition\_1 THEN statements\_1_  
_ELSIF condition\_2 THEN statements\_2_  
_......_

_ELSIF condition\_n THEN statements\_n_  
_\[ELSE else\_statements\]_  
_END IF;_

Where condition_1, condition_2, ..., condition_n are expressions that can produce Boolean values.

The meaning is as follows:

First, evaluate the condition_1 in the IF statement:

*   If the value of condition_1 is true, execute the statements_1 following THEN in the IF statement, then jump to END IF;
*   If the value of condition_1 is false, jump to ELSIF and perform the same operation as in the IF statement;
*   ......
*   If the value of condition_n is true, execute the statements_n following THEN in the ELSIF statement, then jump to END IF;
*   If the value of condition_n is false, proceed to the next statement:

If the next statement is an ELSE statement, execute else_statements and then jump to END IF.

If the next statement is an END IF statement, do nothing and finish.

***Example***

```plsql
DECLARE
i int;
BEGIN
    i := 5;
    IF i < 5 THEN
        i := i + 1;
        DBMS_OUTPUT.PUT_LINE ('This is: '||i);
    ELSIF i>5 THEN
        DBMS_OUTPUT.PUT_LINE ('This is: '|| (i-5));
    ELSE
        DBMS_OUTPUT.PUT_LINE ('This is: '|| (i+5));
    END IF;
END;
/
 
--result
This is: 10
```
