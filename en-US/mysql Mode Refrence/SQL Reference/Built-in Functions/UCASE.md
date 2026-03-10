```ebnf+diagram
ucase::= UCASE "(" expr ")"
```

The UCASE function converts lowercase letters in the value of [expr](../General SQL Syntax/expr) to uppercase.

**expr**

A general expression whose value must be binary type, character type, or other types convertible to character type.

- If expr is of binary type, the function does not perform case conversion and directly returns the original data.

- expr cannot be character data longer than 65534 bytes.

- If the value of expr is NULL, the function returns NULL.

**Return Value Type**

- If expr is of character type, the return value is a corresponding variable-length character type.

- If expr is of binary type other than BLOB, if the return value length is less than 65534, it is of raw type; otherwise, it is of blob type.

- If expr is of BLOB type, the return value is of blob type.

- In other cases, the return value is of VARCHAR type.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT UCASE('Shenzhen nihao') l1, UCASE(NULL) l2 FROM DUAL;
l1                                                        l2
--------------------------------------------------------- -----
SHENZHEN NIHAO  
```
