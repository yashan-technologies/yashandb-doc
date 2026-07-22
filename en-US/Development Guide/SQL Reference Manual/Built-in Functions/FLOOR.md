```ebnf
floor = FLOOR "(" expr ")".
```

The FLOOR function rounds down the value of the given parameter [expr](../General SQL Syntax/expr), and its return type is:

*   When the value of expr is numeric, it returns data of the same type.
*   When the value of expr is character data, it returns data of NUMBER type.
*   When the value of expr is NULL, it returns NULL.

The value of expr can be numeric or character data that can be converted to NUMBER type (conversion failure returns Invalid number error). For other types, the function return type is not supported.

***Example***

```sql
SELECT FLOOR(7) floor1,
FLOOR('0.97') floor2,
FLOOR(6.22) floor3
FROM DUAL;
      FLOOR1      FLOOR2      FLOOR3
------------ ----------- -----------
           7           0           6
```
