```ebnf
trim = TRIM "(" [[LEADING|TRAILING|BOTH] trim_expr from] expr ")" .
```

The TRIM function is used to remove prefixes or suffixes from the source data [expr](../General SQL Syntax/expr) and returns the modified data. The return value type is consistent with the source data.

This function follows the rules below:

- When either trim_expr or expr is of binary type (BINARY, BLOB) or BIT type, the function will process it as binary type and return binary type data. Otherwise, it will process it as string type and return string type data.

- Whether the return value type is LOB depends on whether the length of expr exceeds 65534.

**LEADING|TRAILING|BOTH**

Specifies whether to remove the prefix|suffix|both from the string. It is optional and defaults to BOTH.

**trim\_expr**

Specifies the content of the prefix/suffix. It is optional and defaults to one space. trim_expr is a general expression that must match the type of expr and cannot be of JSON type.

- When the value of trim_expr is NULL, the function returns NULL.

**expr**

expr is a general expression that cannot be of JSON type.

- When the value of expr is NULL, the function returns NULL.

***Example*** for  Heap tables

```sql
SQL> select trim('a!cd' from 'a!cdgklla?cd') res;

res
-------------------------------------------------
gklla?cd                                            

1 row fetched.

SQL> select trim(leading 'a!cd' from 'a!cdgklla?cd') res;

res
-------------------------------------------------
gklla?cd                                       

1 row fetched.

SQL> select trim(trailing 'a!cd' from 'a!cdgklla?cd') res;

res
-------------------------------------------------
a!cdgklla?cd                                      

1 row fetched.
```
