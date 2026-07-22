```ebnf
cot = COT  "(" expr ")" .
```

The COT function returns the cotangent value of the given parameter, where the parameter is an angle expressed in radians and its size is unrestricted (limited only by the range specified by its data type). The function will return a DOUBLE type data.

The value of [expr](../General SQL Syntax/expr) should be numeric or a character type that can be converted to NUMBER type (conversion failure returns Invalid number error). For other types, the function returns type not supported.

If the value of expr is NULL, the function returns NULL.

***Example***

```sql
-- Calculate the cotangent value of the result of expr expressed in radians
SELECT COT(0) res FROM DUAL;
        RES
-----------
        Inf

SELECT COT(45*3.1415926/180) res FROM DUAL;
        RES 
----------- 
   1.0E+000

SELECT COT(60*3.1415926/180) res FROM DUAL;
        RES 
----------- 
 5.774E-001
```
