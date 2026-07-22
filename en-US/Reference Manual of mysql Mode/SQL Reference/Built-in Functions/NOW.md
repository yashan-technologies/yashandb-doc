```ebnf
now = NOW "(" [fsp] ")".
```

The NOW function returns the current date and time value set by the operating system of the database. The return type is TIMESTAMP, formatted as 'YYYY-MM-DD HH24:MI:SS.ff'.

The optional parameter fsp is an integer between 0-6, which indicates the number of significant digits for the fractional seconds in the return value. If the parameter is not specified, it defaults to 0.

The NOW function always returns the time when the statement begins execution. If there are multiple NOW functions within the same SQL statement, the return value of each NOW function will be identical. This behavior is different from the [SYSDATE](SYSDATE) function.

***Example*** for Standalone Deployment Heap tables

```sql
-- example via yasql
SELECT NOW(), SLEEP(1), NOW();

NOW()                             SLEEP(1) NOW(0)
--------------------------- -------------- ---------------------------
2024-12-26 17:59:10.000000                0 2024-12-26 17:59:10.000000

-- example via MySQL Client
SELECT NOW(), SLEEP(1), NOW();
+---------------------+----------+---------------------+
| NOW()               | SLEEP(1) | NOW()               |
+---------------------+----------+---------------------+
| 2025-10-16 15:06:48 |        0 | 2025-10-16 15:06:48 |
+---------------------+----------+---------------------+
```
