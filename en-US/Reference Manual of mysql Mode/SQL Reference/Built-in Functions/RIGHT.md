```ebnf
right = RIGHT "(" expr "," length ")".
```

The RIGHT function extracts a substring of length specified by length from the right side of the source data [expr](../General SQL Syntax/expr) and returns the extracted substring. The return value type is consistent with the source data.

This function follows the rules below:

- When expr is of binary type (BINARY, BLOB) or BIT type, the function will process it as binary type and return a binary type value. Otherwise, it will process it as string type and return a string type value.

- Whether the return value type is LOB depends on whether the minimum value of the expr length and length exceeds 65534.

**expr**

expr is a generic expression, and its value cannot be of JSON type.

- When the value of expr is NULL, the function returns NULL.

**length**

Specifies the length for substring extraction. length is a generic expression that must be convertible to BIGINT, with a value range of [-9223372036854775808,9223372036854775807].

- When the value of length is NULL or in the range of [-9223372036854775808,0], the function returns an empty string.

- When the value of length is a decimal, the function will round it to the nearest integer.
  
- When the length value is greater than the string length of expr, the original string is returned directly.

***Example*** for Standalone Deployment Heap tables

```sql
SQL> select right('abcde', 3) res;

res
-------------- 
cde           

1 row fetched.
```
