```ebnf
SECURITY_MOD_VERSION = SECURITY_MOD_VERSION"()" .
```

SECURITY_MOD_VERSION用于获取并展示密码模块版本信息。

示例

```sql
select SECURITY_MOD_VERSION() from dual;

SECURITY_MOD_VERSION                          
--------------------------------------------- 
崖山数据库管理系统 密码模块 V23              

1 row fetched.
```
