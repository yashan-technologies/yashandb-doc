```ebnf
SECURITY_MOD_STATUS = SECURITY_MOD_STATUS"()" .
```

SECURITY_MOD_STATUS用于获取并展示当前安全模式。

示例

```sql
select SECURITY_MOD_STATUS() from dual;

SECURITY_MOD_STATUS(  
--------------------- 
核准模式状态         

1 row fetched.
```
