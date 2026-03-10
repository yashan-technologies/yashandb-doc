```ebnf+diagram
exp::= EXP "(" expr ")"
```

The EXP function calculates the mathematical result of the number represented by [expr](../General SQL Syntax/expr) with a base of e=2.71828183... and returns a value of type DOUBLE.

The value of expr must be numeric and can be a numeric string; for other types, the function will return a type not supported error.

If expr is NULL, the function returns NULL.

Due to display precision differences, the calculation result of this function can remain consistent with the calculation result of the same function in Oracle for the first 15 digits, after which discrepancies may occur.

***Example***

```sql
SET numwidth 30
SELECT EXP(4.444444) res FROM DUAL;
                            RES 
------------------------------- 
        8.5152519871979379E+001

SELECT EXP(b'0101') res FROM DUAL;
                            RES
------------------------------- 
         1.484131591025766E+002
```
