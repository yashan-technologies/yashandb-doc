```ebnf
rowidtochar = ROWIDTOCHAR "(" expr ")".
```

The ROWIDTOCHAR function converts the ROWID type data represented by [expr](../General SQL Syntax/expr) to a VARCHAR type string data.

This function does not support vectorization calculation.

**expr**

The value of expr must be of ROWID type data or character type data. For other types, the function returns an unsupported type.

When expr is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
SELECT ROWIDTOCHAR('2368:0:0:3164:0') rowid1 FROM DUAL;
ROWID1
-----------------
2368:0:0:3164:0
```
