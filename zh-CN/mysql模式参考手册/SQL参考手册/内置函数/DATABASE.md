```ebnf
database = DATABASE "("")".
```

DATABASE函数返回当前数据库名称。

本函数无入参，返回值为VARCHAR类型。

示例（HEAP表）

```sql
USE SYS;
SELECT DATABASE() res FROM DUAL;
res                                                              
---------------------------------------------------------------- 
SYS
```
