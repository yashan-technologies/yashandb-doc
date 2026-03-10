```ebnf+diagram
trim::= TRIM "(" [[LEADING|TRAILING|BOTH] trim_character from] expr ")" 
```

The TRIM function is used to remove the prefix or suffix from the source string [expr](../General SQL Syntax/expr) and returns a new substring.

**LEADING|TRAILING|BOTH**

Specifies whether to remove the prefix|suffix|both prefix and suffix. This parameter is optional, and the default is BOTH.

**trim_character**

Specifies the content of the prefix/suffix. This parameter is optional, and the default is 1 space. trim_character is a general expression the same as expr, and its value must be of character type or other types that can be converted to character type. In the vectorized execution engine, the length can be multiple characters; otherwise, it can only be 1 character.

When the value of trim_character is NULL, the function returns NULL.

**expr**

A general expression whose value must be of character type or other types (except NCLOB type) that can be converted to character type.

- In the vectorized execution engine, expr cannot be a LOB type for out-of-line storage data.

- When expr is of CLOB type, the return value is of CLOB type; when expr is of NCHAR/NVARCHAR type, the return value is of NVARCHAR type; in other cases, the return value is of VARCHAR type.

- When the value of expr is NULL, the function returns NULL.

***Example***

```sql
SELECT TRIM(' bar ') t_default
,TRIM(LEADING 'x' FROM 'xxxbarxxx') t_left
,TRIM(BOTH 'x' FROM 'xxxbarxxx') t_both
,TRIM(TRAILING 'x' FROM 'xxxbarxxx') t_right
FROM DUAL;
T_DEFAULT T_LEFT    T_BOTH T_RIGHT  
--------- --------- ------ ---------
bar       barxxx    bar    xxxbar
```
