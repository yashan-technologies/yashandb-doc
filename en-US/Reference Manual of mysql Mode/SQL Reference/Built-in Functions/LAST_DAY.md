```ebnf
last_day = LAST_DAY "(" expr ")".
```

The LAST_DAY function returns the date value of the last day of the month for the date represented by [expr](../General SQL Syntax/expr), with a return type of DATE.

**expr**

The value of expr must be of or convertible to the data types DATE/TIMESTAMP/TIME.

When the value of expr is NULL, the function returns NULL.

***Example*** for  Heap tables

```sql
SELECT LAST_DAY('2024-02-01') res FROM DUAL;
res            
--------------------------------
2024-02-29
```
