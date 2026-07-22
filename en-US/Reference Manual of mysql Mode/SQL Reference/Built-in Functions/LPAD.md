```ebnf
lpad = LPAD "(" expr "," pad_length "," pad_expr ")".
```
The LPAD function pads the value of the source data [expr](../General SQL Syntax/expr) from the left side to the specified length of pad_length and fills it with the specified content of pad_expr, returning the newly padded data.

This function follows these rules:

- When either expr or pad_expr is of binary type (BINARY, BLOB) or BIT type, the function will treat it as binary type and the return value will be binary type data; otherwise, it will be treated as string type and the return value will be string type data.

- The return value type being LOB depends on whether pad_length exceeds 65534 bytes.

**expr**

expr is a general expression whose value cannot be of TIME or TIMESTAMP type.

- When the value of expr is NULL, the function returns NULL.

**pad\_length**

Specifies the final length after padding. pad_length is a general expression that must be convertible to BIGINT, with a value range of [-1073741824,1073741824].

- When the value of pad_length is NULL or a negative number, the function returns NULL.

- When the value of pad_length is a decimal, the function will round it to the nearest integer.

- When the pad_length value is less than or equal to the length of expr, it will effectively truncate expr, returning the substring or sub-binary string from left to right based on that length.

 **pad\_character**

Specifies the content to be padded. pad_expr is a general expression that cannot be of JSON type.

- When pad_expr is specified, the function will read the characters or bytes of pad_expr from left to right in a loop and pad them to the left of expr until the length requirement of pad_length is met.

- When pad_expr is an empty string or empty LOB, and the length of expr is less than pad_length, the function returns NULL.

- When the value of pad_expr is NULL, the function returns NULL.

***Example*** for  Heap tables

```sql
SQL> select lpad('a',3,'bcdef') res;

res  
------------------- 
bca                

1 row fetched.

SQL> select lpad('abcdef',3,'g') res;

res 
-------------------- 
abc                 

1 row fetched.
```
