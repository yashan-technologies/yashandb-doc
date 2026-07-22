```ebnf
reverse = REVERSE "(" expr ")".
```

The REVERSE function is used to reverse the value of [expr](../General SQL Syntax/expr).

This function follows the rules below:

- expr cannot be of JSON type.
- If expr is of binary type or bit type, the function will reverse expr by bytes, and the output will be of binary type.
- If expr is of other types, the function will first convert it to character type (a conversion failure will result in an error), and then reverse it by characters, outputting a character type result.
- When expr is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SQL> select reverse('abcd()efgh') res;

res
-----------------------------------------
hgfe)(dcba
```
