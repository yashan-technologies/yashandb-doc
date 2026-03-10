```ebnf+diagram
SECURITY_MOD_VERSION::= SECURITY_MOD_VERSION"()" 
```

SECURITY_MOD_VERSION用于获取并展示密码模块版本信息。

示例

```sql
SELECT SECURITY_MOD_VERSION() FROM dual;

SECURITY_MOD_VERSION                          
--------------------------------------------- 
崖山数据库管理系统 密码模块 V23              

```
