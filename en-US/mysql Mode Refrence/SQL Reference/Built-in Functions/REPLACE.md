```ebnf+diagram
replace::= REPLACE "(" expr "," search_expr ","  replace_expr ")"
```

The REPLACE function replaces all occurrences of the specified content in the source data [expr](../General SQL Syntax/expr) that match search_expr with the content specified in replace_expr and returns the newly replaced data.

This function follows these rules:

- If any of expr, search_expr, or replace_expr is of binary type (BINARY, BLOB) or BIT type, the function will treat it as binary type and the return value will be binary type data. Otherwise, it will treat it as string type and the return value will be string type data.

- Whether the return value type is a LOB depends on whether the length of the replacement result exceeds 65534.

**expr**

expr is a generic expression, and its value cannot be of JSON type.

- If the value of expr is NULL, the function returns NULL.

**search_expr**

The target content to be replaced. search_expr is a generic expression that must match expr and be of character type or any other type that can be converted to character type except JSON type.

- If the value of search_expr is NULL, the function returns NULL.

**replace_expr**

This string value is used for replacement. replace_expr is a generic expression that must match expr and does not support JSON type.

- If the value of replace_expr is NULL, the function returns NULL.

***Example*** for  Heap tables

```sql
SELECT REPLACE('abb','ab','a') res;

res 
----------------------- 
ab                     

```
