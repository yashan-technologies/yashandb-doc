```ebnf+diagram
utc_time::= UTC_TIME "("")"
```

The UTC_TIME function returns the current Coordinated Universal Time (UTC) based on the database's operating system settings. The return value type is TIME, with the default format 'HH24:MI:SS.ff'.

The optional parameter fsp is an integer ranging from 0 to 6, which indicates the number of significant digits for the fractional seconds in the return value. If the parameter is not specified, it defaults to 0.

The UTC_TIME function always returns the current date and time at the moment the statement begins execution. If there are multiple UTC_TIME functions within the same SQL statement, the return values of each UTC_TIME function will be identical.

***Example*** for  Heap tables

```sql
SELECT UTC_TIME(6), SLEEP(1), UTC_TIME(6);

UTC_TIME(6)          SLEEP(1)      UTC_TIME(6)
-------------------- ------------- --------------------
11:08:37.350155      0             11:08:37.350155

```
