CASE is a conditional selection control operator and includes the following two forms:

*   CASE selector WHEN
*   CASE WHEN condition

CASE selector WHEN
------------------

Format:

_CASE selector_  
_WHEN selector\_value\_1 THEN result\_1_  
_WHEN selector\_value\_2 THEN result\_2_  
_..._  
_WHEN selector\_value\_n THEN result\_n_  
_\[ ELSE __else\_result \]_  
_END;_

Where selector, selector\_value\_1, selector\_value\_2, ..., selector\_value\_n are expressions.

Meaning:

First, calculate the value of the selector in the CASE statement. If the value is NULL, skip all WHEN statements and look for the ELSE statement. If an ELSE statement is found, execute else\_result, otherwise raise an error.

If the value of selector is not NULL, compare this value with the first WHEN statement's selector\_value\_1:

*   If the value of selector equals the value of selector\_value\_1, execute the result\_1 after THEN in the first WHEN statement, then jump to END to finish;
*   If the value of selector does not equal the value of selector\_value\_1, jump to the second WHEN statement and perform the same operation as the first WHEN statement;
*   ......
*   If the value of selector equals the value of selector\_value\_n, execute the result\_n after THEN in the nth WHEN statement, then jump to END to finish;
*   If the value of selector does not equal the value of selector\_value\_n, jump to the next statement:

If the next statement is an ELSE statement, execute else\_result, then jump to END to finish.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT CASE 1 WHEN 1 THEN 'one' WHEN 2 THEN 'two' ELSE 'more' END result;
result
------
one
```

CASE WHEN condition
-------------------

Format:

_CASE_  
_WHEN condition\_1 THEN result\_1_  
_WHEN condition\_2 THEN result\_2_  
_..._  
_WHEN condition\_n THEN result\_n_  
_\[ ELSE __else\_result \]_  
_END;_

Where condition\_1, condition\_2, ..., condition\_n are conditions that produce boolean results.

Meaning:

First, evaluate the condition in the first WHEN statement:

*   If the value of condition\_1 is true, execute the result\_1 after THEN in the first WHEN statement, then jump to END to finish;
*   If the value of condition\_1 is false, jump to the second WHEN statement and perform the same operation as the first WHEN statement;
*   ......
*   If the value of condition\_n is true, execute the result\_n after THEN in the nth WHEN statement, then jump to END to finish;
*   If the value of condition\_n is false, jump to the next statement.

If the next statement is an ELSE statement, execute else\_result, then jump to END to finish.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT CASE WHEN 1 > 0 THEN 'true' ELSE 'false' END result;
result
------
true
```
