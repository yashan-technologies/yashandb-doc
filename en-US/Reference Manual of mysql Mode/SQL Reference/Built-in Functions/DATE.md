```ebnf
date = DATE "(" expr ")"..
```

The DATE function is used to extract the date part of the value from [expr](../General SQL Syntax/expr) and return it.

**expr**

The value of expr must be of or convertible to the DATE/TIMESTAMP/TIME data type.

When the value of expr is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT DATE('2024-01-01 01:02:03') RES,TYPEOF(DATE('2024-01-01 01:02:03')) RES_TYPE FROM DUAL;

RES                                              RES_TYPE
------------------------------------------------ ----------------------------------------------------------------
2024-01-01 00:00:00                              date
```
