```ebnf+diagram
curtime::= CURTIME "(" [fsp] ")"
current_time::= CURRENT_TIME "(" [fsp] ")"
```

The CURTIME/CURRENT_TIME function returns the current time based on the operating system settings of the database. The return value type is TIME, and the default format is 'HH24:MI:SS.ff'.

The optional parameter fsp is an integer number from 0 to 6, indicating the number of decimal places in the return value. If the parameter is not specified, the default value is 0.

The CURTIME/CURRENT_TIME function always returns the current time at the moment the statement starts executing. If there are multiple CURTIME/CURRENT_TIME functions in the same SQL statement, each CURTIME/CURRENT_TIME function will return the exact same value.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT CURTIME(6), SLEEP(1), CURRENT_TIME(6);

CURTIME(6)           SLEEP(1)      CURRENT_TIME(6)
-------------------- ------------- --------------------
11:08:37.350155      0             11:08:37.350155

```
