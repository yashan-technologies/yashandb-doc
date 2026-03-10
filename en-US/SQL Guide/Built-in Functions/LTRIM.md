```ebnf+diagram
ltrim::= LTRIM "(" expr ["," trim_character] ")"
```

The LTRIM function removes characters that match trim_character from the left side of the value of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), resulting in a new substring.

**expr**

A generic expression whose value must be of character type or other types that can be converted to character type, excluding NCLOB.

- When expr is of CLOB type, the return value is of CLOB type. When expr is of NCHAR/NVARCHAR type, the return value is of NVARCHAR type. In other cases, the return value is of VARCHAR type.
- In the vectorized execution engine, expr cannot be LOB type external storage data.
- When the value of expr is NULL, the function returns NULL.

**trim_character**

Specifies the content to match and is optional. trim_character is a generic expression that must be of character type or another type that can be converted to character type, the same as expr.

- If trim_character is omitted, the default matching content is one space.
- When trim_character is specified, the function will compare the characters in expr with those in trim_character from left to right. If a character in expr exists in trim_character, it will be removed from expr, stopping at the first character not found in trim_character. If all characters are matched and removed, the function returns an empty string.
- In the vectorized execution engine, trim_character cannot be LOB type external storage data.
- When the value of trim_character is NULL, the function returns NULL.

***Example***

```sql
-- The first three '3's from the left can be found in '33', so they are removed until encountering '1', which cannot be found in '33', stopping the match and returning the remaining string.
SELECT LTRIM('33311333','33') res FROM DUAL;
RES
--------------------
11333    
 
-- All characters from left to right can be found in '313', all are removed, returning an empty string.
SELECT LTRIM('33311333','313') res FROM DUAL;
RES
--------------------
                     
-- When only one parameter is present, matching and removal are executed by space.
SELECT LTRIM(' 11333') res FROM DUAL;
RES
--------------
11333
```
