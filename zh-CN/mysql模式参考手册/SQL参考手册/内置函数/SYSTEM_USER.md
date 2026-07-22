```ebnf
system_user = SYSTEM_USER "("")".
```

SYSTEM_USER函数返回客户端连接数据库使用的用户名与主机名的字符串组合，此函数是[USER](USER)函数的同义词。

本函数无入参，返回值为VARCHAR类型。

示例（HEAP表）

```sql
SELECT SYSTEM_USER() res FROM DUAL;
res               
----------------- 
SYS@localhost
```
