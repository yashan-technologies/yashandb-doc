```ebnf+diagram
dayname::= DAYNAME "("date")"
```

The DAYNAME function returns the name of the day of the week for the specified date.

***Example*** for  Heap tables

```sql
SELECT DAYNAME('2025-8-1') res FROM DUAL;

res               
----------------- 
Friday
```
