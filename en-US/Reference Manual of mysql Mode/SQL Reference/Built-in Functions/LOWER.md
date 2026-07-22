```ebnf
lower = LOWER "(" expr ")".
```

The LOWER function converts uppercase letters in the value of [expr](../General SQL Syntax/expr) to lowercase. This function is a synonym for the [LCASE](LCASE) function.

**expr**

A generic expression whose value must be of binary type, character type, or another type that can be converted to a character type.

- When expr is of binary type, the function does not perform case conversion and directly returns the original data.

- expr must not be character data exceeding 65534 bytes.

- When the value of expr is NULL, the function returns NULL.

**Return Value Type**

- When expr is of character type, the return value is a corresponding variable-length character type.

- When expr is of binary type other than BLOB, if the return value length is less than 65534, it will be of raw type; otherwise, it will be of blob type.

- When expr is of BLOB type, the return value will be of blob type.

- In all other scenarios, the return value will be of VARCHAR type.

***Example*** for  Heap tables

```sql
SELECT LOWER('Shenzhen NIHAO') l1, LOWER(NULL) l2 FROM DUAL;
l1            l2
------------- -----
shenzhen nihao     
```
