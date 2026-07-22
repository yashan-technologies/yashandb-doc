```ebnf
position = POSITION "(" sub_character " IN " expr ")".
```

The POSITION function has the same functionality as the [LOCATE](LOCATE) function, but the input syntax for parameters is different and there is no position parameter. It searches for the target string sub_character starting from the 1st position of the source string [expr](../General SQL Syntax/expr) with a case-sensitive comparison rule, returning the position value of the first occurrence of sub_character. The return value is of BIGINT type; if not found, it returns 0.

***Example*** for Standalone Deployment Heap tables

```sql
select position('b' IN 'abcd') res;

                    res
----------------------- 
                      2

```
