```ebnf+diagram
current_user::= CURRENT_USER "("")"
```

CURRENT_USER函数返回服务器用于对当前客户端进行身份验证的用户名和主机名的字符串组合。

本函数无入参，返回值为VARCHAR类型。

示例（单机HEAP表）

```sql
SELECT CURRENT_USER() res FROM DUAL;
res       
--------- 
SYS@% 
```
