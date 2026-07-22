```ebnf
rtrim = RTRIM "(" expr ["," trim_character] ")".
```

The RTRIM function removes content that matches trim_character from the string represented by [expr](../General SQL Syntax/expr) from right to left, resulting in a new substring.

**expr**

A general expression whose value must be of character type or other types that can be converted to character type, except for NCLOB type.

- When expr is of CLOB type, the return value is of CLOB type. When expr is of NCHAR/NVARCHAR type, the return value is of NVARCHAR type. In other cases, the return value is of VARCHAR type.
- In a vectorized execution engine, expr cannot be LOB type external stored data.
- When the value of expr is NULL, the function returns NULL.

**trim\_character**

Specifies the content to match and is optional. trim_character is a general expression of the same type as expr, required to be of character type or other types that can be converted to character type.

- When trim_character is omitted, the default matching content is one space.
- When trim_character is specified, the function will compare characters in expr and trim_character from left to right. If a character in expr exists in trim_character, it will be removed from expr until the first character not found in trim_character is encountered. If all characters are matched and removed, the function returns an empty string.
- In a vectorized execution engine, trim_character cannot be LOB type external stored data.
- When the value of trim_character is NULL, the function returns NULL.

***Example***

```sql
-- The three '3's from the right can all be found in '33', so they are removed until encountering '1' that cannot be found in '33', stopping the match and returning the remaining string
SELECT RTRIM('33311333','33') res FROM DUAL;
RES       
--------- 
33311     
 
-- All characters from the right can be found in '313', all are removed, returning an empty string
SELECT RTRIM('33311333','313') res FROM DUAL;
RES       
--------- 

-- When there is only one parameter, the matching deletion is performed based on space
SELECT RTRIM('       11333         ') res FROM DUAL;
RES           
------------- 
       11333         
```
