```ebnf
asciistr = ASCIISTR "(" expr ")".
```

The ASCIISTR function converts the string represented by [expr](../General SQL Syntax/expr) to its ASCII-encoded version in the database character set and returns it. Non-ASCII characters will be converted to the form \xxxx, where xxxx represents the UTF-16 code unit.

This function follows the rules below:

*   This function does not support vectorization calculation.
*   The character '\\' will be converted to '\\005C'.
*   ASCII encoded characters will be returned in the database character set encoding version.
*   Non-ASCII encoded characters will be converted to the form \xxxx, where xxxx represents the UTF-16 code unit.
*   When the value of expr is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
SELECT ASCIISTR('HELLO——WORLD') FROM DUAL;

ASCIISTR('HELLO——WORLD')
----------------------------
HELLO\2014\2014WORLD

```
