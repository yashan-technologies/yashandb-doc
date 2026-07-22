```ebnf
rtrim = RTRIM "(" expr ")".
```

The LTRIM function removes spaces from the right side of the source data [expr](../General SQL Syntax/expr) value and returns the newly trimmed data. The return value type is consistent with the source data.

This function follows the rules below:

- When any part of expr is of binary type (BINARY, BLOB) or BIT type, the function will process it as binary type and return a binary type data. Otherwise, it will process and return it as string type data.
- Whether the return value type is LOB depends on whether the expr length exceeds 65534.

**expr**

expr is a general expression whose value cannot be of JSON type.

- When the value of expr is NULL, the function returns NULL.

***Example*** for  Heap tables

```sql

SQL> select length(RTRIM('scdsf   ')) res1,RTRIM('scdsf   ') res2;

                     res1 res2
------------------------- -----------------
                        5 scdsf

1 row fetched.
```
