```ebnf
rpad = RPAD "(" expr "," pad_length "," pad_expr ")".
```
The RPAD function fills the value of the source data [expr](../General SQL Syntax/expr) from the right with the specified length of pad_length and the specified content of pad_expr, then returns the newly padded data.

This function follows these rules:

- If either expr or pad_expr is of binary type (BINARY, BLOB) or BIT type, the function will process it as binary type and the return value will be of binary type data; otherwise, it will be processed as string type and the return value will be of string type data.

- Whether the return value type is LOB depends on whether pad_length exceeds 65534.

**expr**

expr is a generic expression and its value cannot be of TIME or TIMESTAMP type.

- If the value of expr is NULL, the function returns NULL.  

**pad\_length**

Specifies the final length after padding. pad_length is a generic expression that must be of a type that can be converted to BIGINT, with a value range of [-1073741824,1073741824].

- If the value of pad_length is NULL or negative, the function returns NULL.  

- If the value of pad_length is a decimal, the function will round it to the nearest integer.

- If the value of pad_length is less than or equal to the length of expr, it is equivalent to substring expr, and the function returns a substring or sub-binary string of expr truncated from left to right according to this length.

 **pad\_character**

Specifies the content to be padded. pad_expr is a generic expression and its value cannot be of JSON type.

- When pad_expr is specified, the function will read the characters or bytes of pad_expr from left to right in a loop and pad them to the right of expr until the length requirement of pad_length is met.

- If pad_expr is an empty string or an empty LOB, and the length of expr is less than pad_length, the function returns NULL.

- If the value of pad_expr is NULL, the function returns NULL.  

***Example*** for Standalone Deployment Heap tables

```sql
SQL> select rpad('a',3,'bcdef') res;

res
------------------- 
abc                

1 row fetched.

SQL> select rpad('abcdef',3,'g') res;

res
-------------------- 
abc                 

1 row fetched.
```
