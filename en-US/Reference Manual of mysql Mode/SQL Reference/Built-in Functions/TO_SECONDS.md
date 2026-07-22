```ebnf
to_seconds = TO_SECONDS "("date")".
```

The TO_SECONDS function returns the difference in seconds between the specified date and the date '0-1-0'. The date parameter is a data type that can be converted to TIMESTAMP.

***Example*** for  Heap tables

```sql
SELECT TO_SECONDS('2025-8-1') res FROM DUAL;

res               
----------------- 
63921312000
```
