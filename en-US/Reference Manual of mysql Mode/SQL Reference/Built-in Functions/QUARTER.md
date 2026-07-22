```ebnf
quarter = QUARTER "("date")".
```

The QUARTER function returns the quarter of the date specified. The return value range is 1-4.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT QUARTER('2025-8-1') res FROM DUAL;

res               
----------------- 
3
```
