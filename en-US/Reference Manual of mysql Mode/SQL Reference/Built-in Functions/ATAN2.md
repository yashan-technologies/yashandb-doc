```ebnf
atan2 = ATAN2  "(" expr ")" .
```

The ATAN2 function returns the arctangent of the result of the given parameters expr1/expr2, with the parameters expressed in radians. The values themselves are unrestricted (limited only by the prescribed range of their data types), and the function will return a DOUBLE type result within the range of [-pi, pi].

Both [expr](../General SQL Syntax/expr)1 and [expr](../General SQL Syntax/expr)2 must be numeric or character types that can be converted to DOUBLE type. For other types, the function does not support return types.

If either value in expr1 or expr2 is NULL, the function will return NULL.

The BLOB and TEXT type are not allowed to calculate with this function.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT ATAN2(1,1) res FROM DUAL;
        res
-----------
 7.854E-001

SELECT ATAN2(1,2) res FROM DUAL;
        res
-----------
 4.636E-001

SELECT ATAN2(2,2) res FROM DUAL;
        res
-----------
 7.854E-001
```
