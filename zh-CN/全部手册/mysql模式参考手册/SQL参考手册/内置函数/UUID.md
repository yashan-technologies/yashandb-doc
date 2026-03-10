```ebnf+diagram
uuid::= UUID "("")"
```

UUID函数返回一个UUID值，返回值为VARCHAR类型。

示例（单机HEAP表）

```sql
ALTER SESSION SET COMPAT_VECTOR = mysql;
SELECT UUID() res FROM DUAL;
                  res
---------------------
    18472781763704559
```
