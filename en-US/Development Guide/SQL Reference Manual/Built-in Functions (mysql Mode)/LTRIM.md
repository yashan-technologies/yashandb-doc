```ebnf+diagram
ltrim::= LTRIM "(" expr ")"
```

The LTRIM function removes leading spaces from the value of the source data [expr](../General SQL Syntax/expr) and returns the new data after trimming. The return value type is consistent with the source data.

This function follows these rules:

- When any part of expr is of binary type (BINARY, BLOB) or BIT type, the function will handle it as binary and return a binary type result; otherwise, it will handle it as string type and return a string type result.

- Whether the return value type is LOB depends on whether the length of expr exceeds 65534.

**expr**

expr is a generic expression, and its value cannot be of JSON type.

- When the value of expr is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT LENGTH(LTRIM('   scdsf')),LTRIM('   scdsf');

length(LTRIM('   scdsf')) LTRIM('   scdsf')
------------------------- -----------------
                        5 scdsf

```
