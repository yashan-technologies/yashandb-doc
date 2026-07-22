```ebnf
sessiontimezone = SESSIONTIMEZONE ["("  ")"] .
```

The SESSIONTIMEZONE function returns the session's time zone, and the return data type is a string.

This function does not support vectorization calculation.

***Example*** for Heap tables

```sql
select sessiontimezone from dual;

SESSIONTIMEZONE                                                  
---------------------------------------------------------------- 
+08:00 
```
