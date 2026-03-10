```ebnf+diagram
concat_ws::= CONCAT_WS "(" separator "," (expr) {"," (expr)} ")"
```

The CONCAT_WS function concatenates the values of multiple [expr](../General SQL Syntax/expr) using the separator and returns a concatenated string.

This function follows the rules below:

- The number of parameters for this function must be [2,65535].

- If the parameters include binary type or bit type, the function returns a binary type; otherwise, it returns a character type.

- If the separator is NULL, the function returns NULL. If the separator is not NULL, the function skips any NULL expr parameters during concatenation.

***Example*** for  Heap tables

```sql

SELECT CONCAT_WS('1','2', '', 'ab', 'dc') res FROM DUAL;
res   
----- 
21ab1dc 

SELECT CONCAT_WS(12,null,null,'','a',null) res FROM DUAL;
res   
----- 
12a

```
