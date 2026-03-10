## General Description

RENAME is used to rename a table.

## Statement Definition

**rename::=**

```ebnf+diagram
syntax::= RENAME TABLE old_table_name TO new_table_name
```

This statement has the same meanings with `ALTER TABLE old_table_name RANAME [TO | AS] new_table_name`. Please refer to [rename_clause](./ALTER TABLE.html#rename) for more details.