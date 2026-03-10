```ebnf+diagram
subtime::= SUBTIME "(" expr1 "," expr2 ")" 
```

The SUBTIME function adds expr2 to expr1 and returns the result. expr1 is a time or datetime expression, while expr2 is a time expression.

**expr1**

- expr1 must be a [general expression](../General SQL Syntax/expr) recognized by YashanDB and must be convertible to TIME or DATETIME type.

- If expr1 is neither a date-time type nor a numeric type, it is first converted to a string type. If the resulting string contains spaces and can be split into two valid strings, it is converted to DATETIME type; otherwise, it attempts to convert to TIME type. If it fails, it will convert to DATETIME type.

**expr2**

- expr2 must be a [general expression](../General SQL Syntax/expr) recognized by YashanDB and must be convertible to TIME type.

- If expr2 is neither a date-time type nor a numeric type, expr2 is first converted to a string type, and then an attempt is made to convert it to TIME type. If this fails, it will convert to DATETIME type.

***Example*** for  Heap tables

```sql
SELECT SUBTIME('2021-02-03', '11:11:11') res FROM dual;

res
---------------------------------
2021-02-02 12:48:49


```
