```ebnf+diagram
version::= VERSION "("")"
```

VERSION函数返回当前YaShanDB数据库与MySQL兼容的版本信息。

本函数无入参，返回值为VARCHAR类型。

示例（HEAP表）

```sql
SELECT VERSION() res FROM DUAL;
res       
--------- 
5.7.42
```
