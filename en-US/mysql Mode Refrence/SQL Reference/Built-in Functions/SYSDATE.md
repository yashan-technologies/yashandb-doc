```ebnf+diagram
sysdate::= SYSDATE "(" [fsp] ")"
```

The SYSDATE function returns the current date and time value based on the operating system settings of the database. The return type is TIMESTAMP, and the format is 'YYYYMMDD HH:MI:SS'.

The optional parameter fsp is an integer between 0 and 6, which indicates the number of fractional digits in the returned value. If the parameter is not specified, the default is 0.

The SYSDATE function returns the time at which it begins execution. If there are multiple SYSDATE functions in the same SQL statement, each SYSDATE function will return a different value. This feature differs from the [NOW](NOW) function.

***Example*** for Standalone Deployment Heap tables

```sql
-- example via yasql
SELECT SYSDATE(), SLEEP(1), SYSDATE();

SYSDATE()                       SLEEP(1) SYSDATE()
-------------------------- ------------- ---------------------------
2024-12-26 17:52:21.000000             0 2024-12-26 17:52:22.000000

-- example via MySQL Client
SELECT SYSDATE(), SLEEP(1), SYSDATE();
+---------------------+----------+---------------------+
| SYSDATE()           | SLEEP(1) | SYSDATE()           |
+---------------------+----------+---------------------+
| 2025-10-16 15:54:17 |        0 | 2025-10-16 15:54:18 |
+---------------------+----------+---------------------+
```
