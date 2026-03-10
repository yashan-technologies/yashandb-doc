```ebnf+diagram
user::= USER "("")"
```

USER函数返回客户端连接数据库使用的用户名与主机名的字符串组合。

本函数无入参，返回值为VARCHAR类型。

示例（单机HEAP表）

```sql
SELECT USER() res FROM DUAL;
res               
----------------- 
SYS@localhost
```
