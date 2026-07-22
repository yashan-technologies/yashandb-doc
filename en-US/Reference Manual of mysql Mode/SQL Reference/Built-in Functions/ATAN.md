```ebnf
atan = ATAN  "(" expr ")" .
```

The ATAN function returns the arctangent value of the given parameter, with the parameter being an angle expressed in radians. The magnitude itself is unlimited (only restricted by the range defined by its data type). The function returns a DOUBLE type value within the range of [-pi/2, pi/2].

The value of [expr](../General SQL Syntax/expr) can be numeric or character type that can be converted to DOUBLE type. For other types, the function does not support the return type.

When the value of expr is NULL, the function returns NULL.

The BLOB and TEXT type are not allowed to calculate with this function.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT ATAN(-1) res FROM DUAL;
        res
-----------
 -7.85E-001

SELECT ATAN(0) res FROM DUAL;
        res
-----------
          0

SELECT ATAN(1) res FROM DUAL;
        res
-----------
 7.854E-001
```
