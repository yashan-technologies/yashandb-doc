```ebnf
sessiontimezone = SESSIONTIMEZONE ["("  ")"] .
```

SESSIONTIMEZONE函数返回会话的时区，返回的数据类型为字符串。

本函数不支持向量化计算。

示例（HEAP表）

```sql
select sessiontimezone from dual;

SESSIONTIMEZONE                                                  
---------------------------------------------------------------- 
+08:00 
```
