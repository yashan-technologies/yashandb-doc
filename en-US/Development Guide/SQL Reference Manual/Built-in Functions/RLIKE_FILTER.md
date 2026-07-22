```ebnf
rlike_filter = RLIKE_FILTER "(" expr "," regexp ")".
```

RLIKE_FILTER is a regular expression function that matches the target string `regexp` against the source string [expr](../General SQL Syntax/expr) according to the regular pattern and returns the match result. The return value is Boolean type, returning TRUE for a successful match and FALSE otherwise.

This function is similar to the LIKE syntax, but while LIKE can only use the '%' and '_' wildcards, this function allows the target string `regexp` to use regular expressions.

**expr**

A generic expression whose value must be of character type or another type convertible to character type.

- When the value of expr is NULL, the function returns NULL.
- expr cannot be a BFILE or LOB type data exceeding 65534 bytes.
- The function computes the string using the character set defined by the input (only UTF-8 is supported).

**regexp**

Specifies a [RegExp](../General SQL Syntax/RegExp) with a length not exceeding 512 bytes.

When regexp is NULL, the function returns NULL.

***Example***

```sql
SELECT RLIKE_FILTER('aa\naa','^aa$') reg FROM DUAL;
REG                              
--------------------
false             

SELECT RLIKE_FILTER('-654196584','^-[0-9]*[1-9][0-9]*$') reg FROM DUAL;        
REG                                 
--------------------
true
```
