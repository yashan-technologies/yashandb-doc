```ebnf+diagram
strcmp::= STRCMP "(" expr "," expr ")"
```

The STRCMP function is used to compare two [expr](../General SQL Syntax/expr) and return the comparison result.

This function follows these rules:

- Both expr are converted to varchar for comparison.
- According to the current collation order, if the strings are the same, the function returns 0. If the first argument is less than the second argument, the function returns -1; otherwise, it returns 1.
- The current default character set is utf8mb4_general_ci.

***Example*** for Standalone Deployment Heap tables

```sql

SELECT STRCMP('A',65);

STRCMP('A',65) 
-------------- 
             1

```
