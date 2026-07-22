```ebnf
exp = EXP "(" expr ")".
```

The EXP function calculates the mathematical result of the value represented by [expr](../General SQL Syntax/expr) with e=2.71828183... as the base, returning a value of type DOUBLE.

The value of expr must be numeric, which can be a numeric string; for other types, the function returns a type not supported error.

When expr is NULL, the function returns NULL.

The BLOB and TEXT type are not allowed to calculate with this function.

***Example*** for Heap tables

```sql
set numwidth 30
SELECT EXP(4.444444) res FROM DUAL;
                            res 
------------------------------- 
        8.5152519871979379E+001
```
