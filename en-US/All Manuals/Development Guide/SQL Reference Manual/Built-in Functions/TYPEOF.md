```ebnf+diagram
typeof::= TYPEOF "(" expr1 ["," expr2] ")"
```

The TYPEOF function retrieves the data type of the [expr1](../General SQL Syntax/expr) parameter and returns it as a VARCHAR type string.

When the value of expr1 is NULL, the function returns varchar.

The expr2 parameter can take values of 0 and 1, and it cannot be specified in the LSC table.
- 0 indicates that all parameter type information for expr1 (including type precision and string length) and the return value type should be displayed.
- 1 indicates that only the return value type and the corresponding precision or length information should be displayed.

When expr2 is empty, the TYPEOF function only displays the return value type.

***Example***

```sql
SELECT TYPEOF('') t1,
TYPEOF('1.2345') t2,
TYPEOF(1.2345) t3,
TYPEOF(SYSDATE) t4
FROM DUAL;
T1        T2    T3        T4                                                              
--------- ----- --------- ----------------------------------------------------------------
varchar   char  number    date

SELECT TYPEOF(1.21, 0) RES FROM DUAL;

RES
-----------------------------------------
number(3, 2)
[expr_const:number(3, 2)]

SELECT TYPEOF(1.21, 1) RES FROM DUAL;

RES
-------------
number(3, 2)
```
