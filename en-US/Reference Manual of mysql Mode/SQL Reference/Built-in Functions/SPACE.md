```ebnf
space = SPACE "(" expr ")".
```
The SPACE function returns [expr](../General SQL Syntax/expr) number of spaces, with a return type of character type.

This function follows these rules:

- expr cannot be of JSON type.

- If expr is a non-integer type, the function will round it to the nearest integer.

- When the value of expr is greater than 65534, the function returns a value of CLOB type; otherwise, it returns a value of VARCHAR type.

- When the value of expr is a negative number, the function returns a zero-length empty string. When the value of expr is NULL, the function returns NULL.

- The value of expr cannot exceed the maximum value of BIGINT.

***Example*** for Standalone Deployment Heap tables

```sql
SQL> select space(10) res1, length(space(10)) res2 from dual;

res1                                                       res2
----------------------------------------- ---------------------
                                                             10

1 row fetched.
```
