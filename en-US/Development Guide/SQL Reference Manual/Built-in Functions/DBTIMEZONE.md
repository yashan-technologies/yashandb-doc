```ebnf
dbtimezone = DBTIMEZONE ["("  ")"] .
```

The DBTIMEZONE function returns the database's time zone, and the return data type is a string.

***Example***

```sql
select dbtimezone from dual;

DBTIMEZONE
----------
+08:00
```
