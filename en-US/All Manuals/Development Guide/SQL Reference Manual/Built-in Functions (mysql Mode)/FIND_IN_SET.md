```ebnf+diagram
find_in_set::= FIND_IN_SET "(" str_expr ", " str_list_expr ")"
```

The FIND_IN_SET function returns the position of the target string str_expr in the string list str_list_expr, where different strings in the list are separated by `,`. The return type of the position is INTEGER.

**str_list_expr**

Represents a group of string lists, with different strings separated by `,`, which must be of character type or other types that can be converted to character type.

**str_expr**

Represents the string to search for a match in the string list, which must be of character type or other types that can be converted to character type, and is case-sensitive.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT FIND_IN_SET('yashan', 'mysql,YASHAN,yashan,oracle');

find_in_set('yashan', 'mysql,YASHAN,yashan,oracle') 
--------------------------------------------------- 
                                                  3

```
