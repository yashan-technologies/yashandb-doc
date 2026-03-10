```ebnf+diagram
addtime::= ADDTIME "(" expr1 "," expr2 ")" 
```

The ADDTIME function adds expr2 to expr1 and returns the result. expr1 is a time or datetime expression, and expr2 is a time expression.

**expr1**

- expr1 must be a [general expression](../General SQL Syntax/expr) recognized by YashanDB that can be converted to TIME type or DATETIME type.

- If expr1 is neither a datetime type nor a numeric type, it will first be converted to a string type. Based on the converted string, if there are spaces that can divide it into two valid string segments, it will be converted to DATETIME type; otherwise, it will attempt to convert to TIME type, and if that fails, it will convert to DATETIME type.

**expr2**

- expr2 must be a [general expression](../General SQL Syntax/expr) recognized by YashanDB that can be converted to TIME type.

- If expr2 is of DATETIME type, only the TIME portion is used in the calculation.

- If expr2 is neither a datetime type nor a numeric type, it will first convert expr2 to a string type, then attempt to convert to TIME type; if that fails, it will convert to DATETIME type.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT ADDTIME('2021-02-03', '11:11:11') res FROM dual;

res
---------------------------------
2021-02-03 11:11:11

SELECT ADDTIME("1997-12-31 23.59.59.999999", "1998-01-01  01:01:01.99999") res;

res
---------------------------------
1998-01-01 01:01:01.999989

SELECT ADDTIME("1997-12-31 23.59.59.999999", "01:01:01.99999") res;

res
---------------------------------
1998-01-01 01:01:01.999989
```
