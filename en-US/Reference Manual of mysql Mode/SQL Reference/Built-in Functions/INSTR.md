```ebnf
instr = INSTR "(" expr ", " sub_character ")".
```

INSTR function has the same functionality as the [LOCATE](LOCATE) function, but the target string parameter position is different and does not have a position parameter. It searches for the target string sub_character starting from the 1st position of the source string [expr](../General SQL Syntax/expr), with case sensitivity applied. It returns the position value of the first occurrence of sub_character. The return value is of BIGINT type. If not found, it returns 0.

***Example*** for  Heap tables

```sql
select instr('abcd', 'b') res;

res 
--------------------- 
                    2
```
