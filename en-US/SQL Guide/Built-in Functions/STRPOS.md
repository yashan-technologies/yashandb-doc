```ebnf+diagram
strpos::= STRPOS "(" expr "," substring ")"
```

The STRPOS function searches for the target string `substring` in the source string [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) from left to right, returning the position of the first occurrence of `substring` in `expr`, with the return value being of INT type. If no match is found, the function returns 0.

**expr**

A general expression whose value must be of character type or any other type that can be converted to character type, excluding JSON and LOB types.

- When the value of expr is of CHAR type, trailing spaces in the string will be removed first.

- When the value of expr is NULL, the function returns NULL.

**substring**

The string expression to search for. `substring` is a general expression identical to expr, and must also be of character type or any other type that can be converted to character type, excluding JSON and LOB types.

- When the value of substring is of CHAR type, trailing spaces in the string will be removed first. If substring consists of all spaces (for example, `'   '`), the function returns 1.

- When the value of substring is NULL, the function returns NULL.

***Example***

```sql
SELECT STRPOS('abcdef', 'abc') a
,STRPOS('abcdef', 'def') b
,STRPOS('Guangdong——Shenzhen', 'en') c
,STRPOS('  ',  '  ') d
,STRPOS(NULL, NULL) e
FROM DUAL;
           A            B            C            D            E
------------ ------------ ------------ ------------ ------------
           1            4           14            1
```
