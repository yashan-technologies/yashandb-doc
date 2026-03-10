```ebnf+diagram
weekofyear::= WEEKOFYEAR "("date")"
```

The WEEKOFYEAR function returns the week number of the calendar year for the specified date, with a return value range of 1-53. The WEEKOFYEAR function is equivalent to [WEEK](WEEK)(date, 3).

***Example*** for  Heap tables

```sql
SELECT WEEKOFYEAR('2025-8-1') res FROM DUAL;

res               
----------------- 
31
```
