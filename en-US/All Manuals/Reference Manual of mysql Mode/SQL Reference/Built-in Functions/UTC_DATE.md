```ebnf+diagram
utc_date::= UTC_DATE "("")"
```

The UTC_DATE function returns the current Coordinated Universal Time date based on the database's operating system settings. The return value type is DATE, and the default format is 'YYYY-MM-DD'.

The UTC_DATE function always returns the current date at the time the statement begins execution. If there are multiple UTC_DATE functions in the same SQL statement, each UTC_DATE function will return the exact same value.

***Example*** for  Heap tables

```sql
SELECT CURDATE(), SLEEP(1), CURDATE();

UTC_DATE()           SLEEP(1)      UTC_DATE()
-------------------- ------------- --------------------
2024-12-26           0             2024-12-26

```
