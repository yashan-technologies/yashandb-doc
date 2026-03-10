```ebnf+diagram
cos::= COS  "(" expr ")" 
```

The COS function returns the cosine value of the given parameter, where the parameter is an angle expressed in radians. The magnitude is essentially unlimited (only constrained by the range defined by its data type), and the function will return a DOUBLE type value within the interval [-1,1].

The value of [expr](../General SQL Syntax/expr) can be numeric or a string that can be converted to DOUBLE type. For other types, the function's return type is not supported.

When the value of expr is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT COS(30*3.1415926/180) res FROM DUAL;
        RES 
----------- 
  8.66E-001
    
SELECT COS(45*3.1415926/180) res FROM DUAL;
        RES 
----------- 
 7.071E-001
    
SELECT COS(60*3.1415926/180) res FROM DUAL;
        RES 
----------- 
   5.0E-001
```
