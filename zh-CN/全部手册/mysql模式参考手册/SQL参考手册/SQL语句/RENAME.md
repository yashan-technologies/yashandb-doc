## 通用描述

RENAME用于表进行重命名。

## 语句定义

**rename::=**

```ebnf+diagram
syntax::= RENAME TABLE old_table_name TO new_table_name
```

该语句含义与`ALTER TABLE old_table_name RANAME [TO | AS] new_table_name`含义相同，请参考[rename_clause](ALTER TABLE.html#rename)了解详细信息。
