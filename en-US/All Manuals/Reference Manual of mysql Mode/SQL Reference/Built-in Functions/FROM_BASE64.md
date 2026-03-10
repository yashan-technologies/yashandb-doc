```ebnf+diagram
from_base64::= FROM_BASE64 "(" expr ")"
```

The FROM_BASE64 function returns the raw data decoded from the given parameter [expr](../General SQL Syntax/expr) according to BASE-64 rules. The decoding can recognize and ignore newlines, carriage returns, tabs, and spaces.

**expr**

A general expression whose value must be of binary type, character type, or another type that can be converted to character type.

- If there are characters other than the equal sign `=` and ignorable characters after the first equal sign in expr, the function returns NULL.
- If the byte count of expr after removing ignorable characters is not a multiple of 4, the function returns NULL.
- If expr contains characters outside the BASE-64 encoding range, the function returns NULL.
- If the value of expr is NULL, the function returns NULL.

**Return Type**

- When expr is of character type, the return type is VARCHAR.
- When expr is a binary type other than BLOB, the return type is raw if the length is less than 65534, otherwise it is blob.
- When expr is of BLOB type, the return type is blob.
- When expr is of CLOB, NCLOB, or JSON type, the return type is CLOB.
- In other cases, the return type is VARCHAR.

***Example*** for  Heap tables

```sql
SELECT from_base64('dGVzdA==') res;

res 
----------------------- 
test                   

```
