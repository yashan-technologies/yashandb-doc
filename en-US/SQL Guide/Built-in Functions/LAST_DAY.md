```ebnf+diagram
last_day::= LAST_DAY "(" expr ")"
```

The LAST_DAY function returns the last day of the month for the date value represented by [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), with a return type of DATE, consistent with the format specified by the DATE_FORMAT parameter.

**expr**

The value of expr must be of type DATE, TIME, TIMESTAMP, timezone type, or a character type that can be converted to DATE. When it is of type TIME, the function returns NULL.

If the value of expr is NULL, the function returns NULL.

***Example***

```sql
SELECT LAST_DAY('2020-02-01') res FROM DUAL;
RES
------------------------------------------------
2020-02-29 00:00:00
                                 
SELECT LAST_DAY(SYSDATE+20) res FROM DUAL;
RES            
--------------------------------
2021-12-31 18:14:21
```
