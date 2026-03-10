```ebnf+diagram
utc_timestamp::= UTC_TIMESTAMP "(" [fsp] ")"
```

The UTC_TIMESTAMP function returns the current Coordinated Universal Time (UTC) set by the operating system of the database. The return value type is TIMESTAMP, formatted as 'YYYYMMDD HH:MI:SS'.

The optional parameter fsp is an integer from 0 to 6, indicating the number of digits of precision for the fractional seconds in the return value. If the parameter is not specified, the default is 0.

The UTC_TIMESTAMP function always returns the UTC time at the moment the statement begins execution. If there are multiple UTC_TIMESTAMP functions within the same SQL statement, each UTC_TIMESTAMP function's return value will be identical.

***Example*** for Standalone Deployment Heap tables

```sql
-- example via yasql

SELECT UTC_TIMESTAMP(), SLEEP(1), UTC_TIMESTAMP();

UTC_TIMESTAMP()                                                               SLEEP(1) UTC_TIMESTAMP()              
---------------------------------------------------------------- --------------------- ----------------------------------------------------------------
2025-10-16 08:07:40.000000                                                           0 2025-10-16 08:07:40.000000   

-- example via MySQL Client
SELECT UTC_TIMESTAMP(), SLEEP(1), UTC_TIMESTAMP();
+---------------------+----------+---------------------+
| UTC_TIMESTAMP()     | SLEEP(1) | UTC_TIMESTAMP()     |
+---------------------+----------+---------------------+
| 2025-10-16 08:07:55 |        0 | 2025-10-16 08:07:55 |
+---------------------+----------+---------------------+
```
