```ebnf+diagram
nls_lower::= NLS_LOWER "(" expr ["," expr ] ")"
```

The NLS_LOWER function converts uppercase letters in the value of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) to lowercase according to the specified collation. When the second parameter is omitted, NLS_LOWER is equivalent to LOWER.

This function does not support vectorization calculation.

**Initial Data expr**

The initial data to be converted, a general expression, must be of character type or another type that can be converted to character type.

- expr cannot be character data exceeding 65534 bytes.

- When the value of expr is NULL, the function returns NULL.

**Collation expr**

The second expr can specify collation, formatted as `"NLS_SORT=collation"`, which is optional and defaults to `"NLS_SORT=CHINESE_PINYIN"`. Currently, only the following collations are supported for converting data to lowercase:

- CHINESE_PINYIN: Case-sensitive pinyin collation.

- CHINESE_PINYIN_CI: Case-insensitive pinyin collation.

- BINARY: Binary encoding collation.

**Return Value Type**

- When the initial data expr is of character type, the return value is a corresponding variable-length character type.

- When the initial data expr is of CLOB/NCLOB type, the return value is of the same type as the initial data.

- In other scenarios, the return value is VARCHAR type.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT NLS_LOWER('AaA') RES FROM DUAL;

RES
-----
aaa

SELECT NLS_LOWER('AaA','NLS_SORT=CHINESE_PINYIN') RES FROM DUAL;

RES
-----
aaa
```
