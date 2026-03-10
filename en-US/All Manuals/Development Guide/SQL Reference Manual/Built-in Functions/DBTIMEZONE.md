```ebnf+diagram
dbtimezone::= DBTIMEZONE ["("  ")"] 
```

The DBTIMEZONE function returns the database's time zone, and the return data type is a string.

***Example***

```sql
SELECT dbtimezone FROM dual;

DBTIMEZONE
----------
+08:00
```
