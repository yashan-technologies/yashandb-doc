```ebnf
isnull = ISNULL "(" expr ")".
```

The ISNULL function checks whether the value of the parameter [expr](../General SQL Syntax/expr) is NULL and returns true or false.

The value of expr can be any data type except ROWID and UDT types.

Both empty strings and NULL are treated as true.

***Example***

```sql
SELECT ISNULL('') nulla, ISNULL(3-3) nullb, ISNULL('      ') nullc FROM DUAL;
NULLA                NULLB                NULLC               
-------------------- -------------------- --------------------
true                 false                false
```
