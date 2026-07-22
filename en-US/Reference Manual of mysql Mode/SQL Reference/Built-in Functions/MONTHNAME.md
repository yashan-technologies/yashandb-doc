```ebnf
monthname = MONTHNAME "("date")".
```

The MONTHNAME function returns the name of the month of the date specified.

***Example*** for  Heap tables

```sql
SELECT MONTHNAME('2025-8-1') res FROM DUAL;

res               
----------------- 
August
```
