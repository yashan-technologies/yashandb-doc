```ebnf
unistr = UNISTR "(" expr ")".
```

The UNISTR function is used to convert the Unicode encoded parts of the source string [expr](../General SQL Syntax/expr) into corresponding characters, and returns a string of NVARCHAR type.

This function follows the rules below:

- This function does not support vectorization calculation.

- The Unicode encoding is limited to UCS-2 format, which is represented as '\' followed by 4 hexadecimal digits.

- To include a backslash in the string itself, another backslash must be placed before it; formats that do not conform will result in an error.

- Maximum character length returned by the function:

  - When as a constant, it returns the original string's character length.

  - When as a column field, it returns the maximum character length.

**expr**

A generic expression whose value must be character type or can be converted to character type.

- When expr is of NCHAR/NVARCHAR type, the function converts each byte to UTF16 type.

- When expr is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
SELECT UNISTR('\1f603\1f604') FROM DUAL;
UNISTR('\1F603\1F604')
----------------------
ὠ3ὠ4
      
SELECT UNISTR('\\1f603\1f604') FROM DUAL;
UNISTR('\\1F603\1F604')
---------------------------------
\1f603ὠ4

SELECT UNISTR('\\\1f603\1f604') FROM DUAL;
UNISTR('\\\1F603\1F604')
-------------------------
\ὠ3ὠ4
```
