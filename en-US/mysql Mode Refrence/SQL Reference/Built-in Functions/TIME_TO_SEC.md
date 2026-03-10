```ebnf+diagram
time_to_sec::= TIME_TO_SEC "("time")"
```

The TIME_TO_SEC function converts the time specified by the time parameter into a value in seconds and returns it. The time parameter must be of a data type that can be converted to TIME.

***Example*** for  Heap tables

```sql
SELECT TIME_TO_SEC('2025-8-1 13:24:56') res FROM DUAL;

res               
----------------- 
48296
```
