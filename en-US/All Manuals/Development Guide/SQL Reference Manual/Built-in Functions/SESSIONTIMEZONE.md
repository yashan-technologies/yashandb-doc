```ebnf+diagram
sessiontimezone::= SESSIONTIMEZONE ["("  ")"] 
```

The SESSIONTIMEZONE function returns the session's time zone, and the return data type is a string.

This function does not support vectorization calculation.

***Example*** for Heap tables

```sql
SELECT sessiontimezone FROM dual;

SESSIONTIMEZONE                                                  
---------------------------------------------------------------- 
+08:00 
```
