```ebnf
lcase = LCASE "(" expr ")".
```

The LCASE function converts uppercase letters in the value of [expr](../General SQL Syntax/expr) to lowercase.

**expr**

A general expression whose value must be of binary type, character type, or other types that can be converted to character type.

- When expr is of binary type, the function does not perform case conversion and returns the original data directly.

- expr cannot be character data exceeding 65534 bytes.

- When the value of expr is NULL, the function returns NULL.

**Return Value Type**

- When expr is of character type, the return value is a corresponding variable-length character type.

- When expr is of binary type other than BLOB, if the return value length is less than 65534, it will be of raw type; otherwise, it will be of blob type.

- When expr is of BLOB type, the return value is of blob type.

- In other cases, the return value will be of VARCHAR type.

***Example*** for  Heap tables

```sql
SELECT LCASE('Shenzhen NIHAO') l1, LCASE(NULL) l2 FROM DUAL;
l1                                                        l2
--------------------------------------------------------- -----
shenzhen nihao 
```
