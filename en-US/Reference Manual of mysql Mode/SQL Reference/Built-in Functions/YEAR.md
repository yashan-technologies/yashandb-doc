```ebnf
year = YEAR "(" expr ")" .
```

The YEAR function is used to calculate the year information of [expr](../General SQL Syntax/expr), returning an INT type value.

**expr**

The value of expr must be of TIMESTAMP type or another type that can be converted to TIMESTAMP type.

When the value of expr is NULL, the function returns NULL.

***Example*** for  Heap tables

```sql
SELECT YEAR('2022-7-26') res;
         res
------------
        2022 

SELECT YEAR('00-10-28 23:59:59.999999999') res FROM DUAL;
         res
------------
        2000
```
