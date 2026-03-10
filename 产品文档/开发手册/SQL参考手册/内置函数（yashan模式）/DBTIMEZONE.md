```ebnf+diagram
dbtimezone::= DBTIMEZONE ["("  ")"] 
```

DBTIMEZONE函数返回数据库的时区，返回的数据类型为字符串。


示例

```sql
SELECT dbtimezone FROM dual;

DBTIMEZONE
----------
+08:00
```