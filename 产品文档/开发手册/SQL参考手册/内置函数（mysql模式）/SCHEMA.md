```ebnf+diagram
schema::= SCHEMA "("")"
```

SCHEMA函数返回当前数据库名称，是[DATABASE](DATABASE)函数的同义词。

本函数无入参，返回值为VARCHAR类型。

示例（单机HEAP表）

```sql
USE SYS;
SELECT SCHEMA() res FROM DUAL;
res                                                              
---------------------------------------------------------------- 
SYS
```