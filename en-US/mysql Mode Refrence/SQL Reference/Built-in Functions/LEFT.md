```ebnf+diagram
left::= LEFT "(" expr "," length ")"
```

The LEFT function extracts a substring from the left of the source data [expr](../General SQL Syntax/expr) with the length specified by length and returns the extracted substring. The return value type is consistent with the source data.

This function follows these rules:

- When expr is of binary type (BINARY, BLOB) or BIT type, the function processes it as binary type and returns a binary type value; otherwise, it processes it as string type and returns a string type value.

- Whether the return value type is LOB depends on whether the minimum value of the length of expr and length exceeds 65534.

**expr**

expr is a general expression whose value cannot be of JSON type.

- When the value of expr is NULL, the function returns NULL.

**length**

Specifies the length of the substring to be extracted. Length is a general expression that must be of a data type that can be converted to BIGINT, with a value range of [-9223372036854775808, 9223372036854775807].

- When the value of length is NULL or in the range [-9223372036854775808, 0], the function returns an empty string.

- When the value of length is a decimal, the function rounds it to the nearest integer.
  
- When the value of length is greater than the length of the expr string, it returns the original string directly.

***Example*** for  Heap tables

```sql
SELECT LEFT('abcde', 3) res;

res 
-------------- 
abc           

```
