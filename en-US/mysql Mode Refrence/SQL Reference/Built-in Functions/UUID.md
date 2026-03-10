```ebnf+diagram
uuid::= UUID "("")"
```

The UUID function returns a UUID value, and the return type is VARCHAR.

***Example*** for Standalone Deployment Heap tables

```sql
ALTER SESSION SET COMPAT_VECTOR = mysql;
SELECT UUID() res FROM DUAL;
                  res
---------------------
    18472781763704559
```
