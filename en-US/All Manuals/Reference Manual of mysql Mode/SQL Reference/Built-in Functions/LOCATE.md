```ebnf+diagram
locate::= LOCATE "(" sub_character "," expr ["," position] ")"
```

The LOCATE function searches for the target string `sub_character` starting from the `position` in the source string [`expr`](../General SQL Syntax/expr). The comparison is case-sensitive, and it returns the position value of the first occurrence of `sub_character`. The return type is BIGINT; if not found, it returns 0.

**expr, sub_character**

`expr` specifies the source string, and `sub_character` specifies the target string. Both are general expressions and must be of character type, binary type, or other types that can be converted to character type.

- When the value of `expr` is NULL, the function returns NULL.

- When the value of `sub_character` is NULL, the function returns NULL.

- Both cannot be LOB type data exceeding 65534 bytes.

**position**

Specifies the offset for starting the search (i.e., starting position), which is optional and defaults to 1. `position` is a general expression of the same type as `expr`, and must be numeric data or other types that can be converted to NUMBER type.

- The value of `position` should be an integer or convertible to an integer (floating point types or NUMBER types with decimals will be rounded). Internally, it is uniformly converted to BIGINT type. Positive integers indicate searching from the starting position, while 0 or negative integers will not perform the search and will return 0.

- When `position` is NULL, the function returns 0.

***Example*** for  Heap tables

```sql
SELECT LOCATE('c', 'abcd', null) res;

                      res 
------------------------- 
                        0

SELECT LOCATE('b', 'abcd') res;

                  res 
--------------------- 
                    2
```
