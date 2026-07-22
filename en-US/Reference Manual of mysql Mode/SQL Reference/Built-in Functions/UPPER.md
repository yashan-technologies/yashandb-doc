```ebnf
upper = UPPER "(" expr ")".
```

The UPPER function converts lowercase letters in the value of [expr](../General SQL Syntax/expr) to uppercase. This function is a synonym for the [UCASE](UCASE) function.

**expr**

A general expression that must be of binary type, character type, or another type that can be converted to character type.

- When expr is of binary type, the function does not perform case conversion and directly returns the original data.

- expr cannot be character data exceeding 65534 bytes.

- When the value of expr is NULL, the function returns NULL.

**Return Value Type**

- When expr is of character type, the return value is a corresponding variable-length character type.

- When expr is of binary type other than BLOB, if the return value length is less than 65534, it will be of raw type; otherwise, it will be of blob type.

- When expr is of BLOB type, the return value is of blob type.

- In other scenarios, the return value is of VARCHAR type.

***Example*** for  Heap tables

```sql
SELECT UPPER('Shenzhen nihao') l1, UPPER(NULL) l2 FROM DUAL;
l1                                                        l2
--------------------------------------------------------- -----
SHENZHEN NIHAO
```
