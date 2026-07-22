```ebnf
uuid = UUID "("")".
```

The UUID function returns a UUID value, and the return type is VARCHAR.

***Example*** for  Heap tables

```sql
alter session set COMPAT_VECTOR = mysql;
SELECT UUID() res FROM DUAL;
                  res
---------------------
    18472781763704559
```
