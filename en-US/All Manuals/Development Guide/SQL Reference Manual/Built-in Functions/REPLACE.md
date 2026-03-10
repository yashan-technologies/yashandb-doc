```ebnf+diagram
replace::= REPLACE "(" expr "," search_character [","  replace_character] ")"
```

The REPLACE function replaces all instances of search_character in the source string [expr](../General SQL Syntax/expr) with replace_character, returning a new string.

**expr**

The value of expr must be of character type, CLOB/NCLOB/BFILE, or any other type convertible to character type excluding JSON and BLOB.

- When the value of `expr` is of type CLOB/NCLOB, the function returns a value of the same type as `expr`; in all other cases, the function returns a value of type VARCHAR.

- When the value of expr is NULL, the function returns NULL.

**search_character**

The string to be replaced, search_character is a generic expression that must be of character type or any other type convertible to character type excluding JSON and BLOB.

When the value of search_character is NULL, the function performs no replacements.

**replace_character**

The string used for replacement, replace_character is a generic expression that must be of character type or any other type convertible to character type excluding JSON and BLOB.

- When the value of replace_character is NULL, the function will remove the search_character portion from expr.

- If replace_character is not specified, the default replacement value is NULL.

***Example***

```sql
SELECT REPLACE('ShenZhen', 'Zhen', 'Yang') REPLACE FROM DUAL;
REPLACE  
---------
ShenYang
 
--No replacement performed
SELECT REPLACE('ShenZhen', '', 'Yang') REPLACE FROM DUAL;
REPLACE  
---------
ShenZhen
 
--When the replacement value is empty, remove the string to be replaced
SELECT REPLACE('ShenZhen', 'Zhen') REPLACE FROM DUAL;
REPLACE
-------
Shen
```
