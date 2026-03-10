```ebnf+diagram
uuid_short::= UUID_SHORT "("")"
```

UUID_SHORT函数返回一个UUID_SHORT值，返回值为BIGINT UNSIGNED类型。

示例（HEAP表）

```sql
ALTER SESSION SET COMPAT_VECTOR = mysql;
SELECT UUID_SHORT() res FROM DUAL;
res
---------------------
    18472781763704559
```
