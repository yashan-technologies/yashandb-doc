```ebnf+diagram
connection_id::= CONNECTION_ID "("")"
```

CONNECTION_ID函数返回当前会话ID，返回值为BIGINT类型。

该函数返回值与`INFORMATION_SCHEMA.PROCESSLIST`的ID列或`SHOW PROCESSLIST`命令输出中的ID列同源。

示例（HEAP表）

```sql
 SELECT CONNECTION_ID() AS id3;

                  id3
---------------------
                   34
```
