CASE is a conditional selection control operator and includes the following two forms:

*   CASE selector WHEN
*   CASE WHEN condition

CASE selector WHEN
------------------

Format:

_CASE selector_  
_WHEN selector_value_1 THEN result_1_  
_WHEN selector_value_2 THEN result_2_  
_..._  
_WHEN selector_value_n THEN result_n_  
_\[ ELSE __else_result \]_  
_END;_

Where selector, selector_value_1, selector_value_2, ..., selector_value_n are expressions.

Meaning:

First, calculate the value of the selector in the CASE statement. If the value is NULL, skip all WHEN statements and look for the ELSE statement. If an ELSE statement is found, execute else_result, otherwise raise an error.

If the value of selector is not NULL, compare this value with the first WHEN statement's selector_value_1:

*   If the value of selector equals the value of selector_value_1, execute the result_1 after THEN in the first WHEN statement, then jump to END to finish;
*   If the value of selector does not equal the value of selector_value_1, jump to the second WHEN statement and perform the same operation as the first WHEN statement;
*   ......
*   If the value of selector equals the value of selector_value_n, execute the result_n after THEN in the nth WHEN statement, then jump to END to finish;
*   If the value of selector does not equal the value of selector_value_n, jump to the next statement:

If the next statement is an ELSE statement, execute else_result, then jump to END to finish.

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
_WHEN condition_1 THEN result_1_  
_WHEN condition_2 THEN result_2_  
_..._  
_WHEN condition_n THEN result_n_  
_\[ ELSE __else_result \]_  
_END;_

Where condition_1, condition_2, ..., condition_n are conditions that produce boolean results.

Meaning:

First, evaluate the condition in the first WHEN statement:

*   If the value of condition_1 is true, execute the result_1 after THEN in the first WHEN statement, then jump to END to finish;
*   If the value of condition_1 is false, jump to the second WHEN statement and perform the same operation as the first WHEN statement;
*   ......
*   If the value of condition_n is true, execute the result_n after THEN in the nth WHEN statement, then jump to END to finish;
*   If the value of condition_n is false, jump to the next statement.

If the next statement is an ELSE statement, execute else_result, then jump to END to finish.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT CASE WHEN 1 > 0 THEN 'true' ELSE 'false' END result;
result
------
true
```
