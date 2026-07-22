```ebnf
uuid = UUID "("")".
```

UUID函数返回一个UUID值，返回值为VARCHAR类型。

示例（单机HEAP表）

```sql
alter session set COMPAT_VECTOR = mysql;
SELECT UUID() res FROM DUAL;
                  res
---------------------
    18472781763704559
```
