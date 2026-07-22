```ebnf
acos = ACOS  "(" expr ")" .
```

The ACOS function calculates the arccosine of the given parameter, which is expressed in radians and must be within the range of [-1,1]. The function will return a DOUBLE type value in the range of [0,pi].

The value of [expr](../General SQL Syntax/expr) can be either numeric or a character type that can be converted to DOUBLE. For other types, the function does not support return types.

When the value of expr is NULL, the function returns NULL.

The BLOB and TEXT type are not allowed to calculate with this function.

***Example*** for Heap tables

```sql
SELECT ACOS(-1) res FROM DUAL;
        res
-----------
 3.142E+000

SELECT ACOS(0) res FROM DUAL;
        res
-----------
 1.571E+000

SELECT ACOS(1) res FROM DUAL;
        res
-----------
          0
```
