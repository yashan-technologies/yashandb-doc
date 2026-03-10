```ebnf+diagram
instr::= INSTR "(" expr "," sub_character ["," position ["," occurrence]] ")"
```

The INSTR function searches for the target string `sub_character` starting from the `position`th character of the source string `expr` [expr](../General SQL Syntax/expr) and returns the position of the `occurrence`-th occurrence of `sub_character`. The return value is of BIGINT type. If not found, it returns 0.

**expr, sub_character**

`expr` specifies the source string, and `sub_character` specifies the target string. Both are general expressions. `expr` must be of character type, CLOB, NCLOB, or other types that can be converted to character type. `sub_character` must be of character type or other types that can be converted to character type.

- When `expr` is NULL, the function returns NULL.

- When `sub_character` is NULL, the function returns NULL.

- When `expr` is empty_clob(), if `sub_character` is NULL or implicitly converts to NULL, the function returns NULL; otherwise, it returns 0.

- When `expr` is empty_blob(), the function returns NULL.

**position**

Specifies the offset for starting the search (i.e., the starting position). It is optional and defaults to 1. `position` is a general expression that must be of numeric data type or other types convertible to NUMBER type.

- The value of `position` should be an integer or convertible to an integer, with a range of [-9223372036854775808, 9223372036854775807]. Positive integers indicate searching forward from the starting position, while negative integers indicate searching backward from the starting position.

    - When the value of `position` is a NUMBER with a decimal, the function truncates the decimal and retains the integer part.

    - When the value of `position` is of floating-point type, if `expr` is of CLOB, NCLOB, BLOB, or JSON type, the function truncates `position` directly to an integer. If `expr` is of other types, the function rounds `position` using odd/even rounding.

- When the value of `position` is 0, the function does not perform the search and returns 0.

- When `position` is NULL, the function returns NULL.

**occurrence**

Specifies which occurrence of the substring in `expr` to return. It is optional and defaults to 1. `occurrence` is a general expression that must be of numeric data type or other types convertible to NUMBER type.

- The value of `occurrence` must be a positive integer or convertible to a positive integer, with a range of [1, 9223372036854775807].

    - When the value of `occurrence` is a NUMBER with a decimal, the function truncates the decimal and retains the integer part.

    - When the value of `occurrence` is of floating-point type, the function rounds it to the nearest integer.

- When `occurrence` is NULL, the function returns NULL.  

***Example***

```sql
SELECT INSTR('This is a playlist', 'is') POS FROM DUAL;
                  POS
---------------------
                    3
 
SELECT INSTR('This is a playlist', 'is', 1, 2) POS FROM DUAL;
                  POS
---------------------
                    6
 
SELECT INSTR('This is a playlist', 'is', 1, 3) POS FROM DUAL;
                  POS
---------------------
                   16
 
SELECT INSTR('This is a playlist', 'is', -2, 3) POS FROM DUAL;
                  POS
---------------------
                    3
 
SELECT INSTR('This is a playlist', 'is', 5, 3) POS FROM DUAL;
                  POS
---------------------
                    0
```
