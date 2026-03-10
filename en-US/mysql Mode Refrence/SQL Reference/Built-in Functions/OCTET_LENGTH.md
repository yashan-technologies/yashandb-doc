```ebnf+diagram
octet_length::= OCTET_LENGTH "(" expr ")"
```

The OCTET_LENGTH function counts the length of the value of [expr](../General SQL Syntax/expr) and returns a BIGINT value. This function is synonymous with the LENGTH function. Please refer to [LENGTH](LENGTH) for usage.