```ebnf
weekday = WEEKDAY "("date")".
```

The MONTHNAME function returns the index of the day of the week for the specified date, where 0 represents Monday, 1 represents Tuesday, and 6 represents Sunday, and so on.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT WEEKDAY('2025-8-1') res FROM DUAL;

res               
----------------- 
4
```
