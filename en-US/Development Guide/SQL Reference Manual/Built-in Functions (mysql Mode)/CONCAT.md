```ebnf+diagram
concat::= CONCAT "(" expr "," (expr) {"," (expr)} ")"
```

The CONCAT function concatenates the values of multiple [expr](../General SQL Syntax/expr) into a single string, equivalent to `expr||expr||……`.

This function follows these rules:

- The function must have [1,65535] parameters.
- If any parameter is of binary type or bit type, the function returns a binary type; otherwise, it returns a character type.
- If any parameter is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT CONCAT('a','!=','bcd') res FROM dual;

res
-----------------------------------------
a!=bcd

```
