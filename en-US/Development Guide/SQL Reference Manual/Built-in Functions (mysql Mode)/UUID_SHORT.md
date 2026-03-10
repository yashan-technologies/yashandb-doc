```ebnf+diagram
uuid_short::= UUID_SHORT "("")"
```

The UUID_SHORT function returns a UUID_SHORT value, which is of BIGINT UNSIGNED type.

***Example*** for Standalone Deployment Heap tables

```sql
ALTER SESSION SET COMPAT_VECTOR = mysql;
SELECT UUID_SHORT() res FROM DUAL;
res
---------------------
    18472781763704559
```
