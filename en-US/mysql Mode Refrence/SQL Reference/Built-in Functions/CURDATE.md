```ebnf+diagram
curdate::= CURDATE "("")"
current_date::= CURRENT_DATE "("")"
```

The CURDATE/CURRENT_DATE function returns the current date set by the operating system of the database, with a return type of DATE and a default format of 'YYYY-MM-DD'.

The CURDATE/CURRENT_DATE function always returns the current date at the time the statement begins execution. If there are multiple CURDATE/CURRENT_DATE functions in the same SQL statement, the return values of each CURDATE/CURRENT_DATE function will be identical.

***Example*** for  Heap tables

```sql
SELECT CURDATE(), SLEEP(1), CURDATE();

CURDATE()            SLEEP(1)      CURRENT_DATE()
-------------------- ------------- --------------------
2024-12-26           0             2024-12-26

```
