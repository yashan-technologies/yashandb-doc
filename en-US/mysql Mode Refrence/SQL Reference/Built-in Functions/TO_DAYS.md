```ebnf+diagram
to_days::= TO_DAYS "("date")"
```

The TO_DAYS function returns the difference in days between the specified date and the date '0-1-0'. The date parameter must be of a data type that can be converted to TIMESTAMP.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT TO_DAYS('2025-8-1') res FROM DUAL;

res               
----------------- 
739830
```
