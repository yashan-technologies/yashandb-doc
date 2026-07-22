```ebnf
instrb = INSTRB "(" expr "," sub_character ["," position ["," occurrence]] ")".
```
The INSTRB function starts searching for the target string sub_character from the position byte of the source string [expr](../General SQL Syntax/expr) and returns the position value of the occurrence of sub_character for the occurrence-th time. The return value is of BIGINT type. If not found, it returns 0.

This function does not support vectorization calculation.

**expr, sub_character**

expr specifies the source string, and sub_character specifies the target string. Both are general expressions, and their values must be of character type or other types that can be converted to character type.

- expr and sub_character cannot be BFILE or LOB data types that exceed 65534 bytes.

- When the value of expr is NULL, the function returns NULL.

- When the value of sub_character is NULL, the function returns NULL.

**position**

Specifies the starting offset (i.e., starting position) for searching, which is optional and defaults to 1. position is a general expression the same as expr, and its value must be numeric or other types that can be converted to NUMBER type.

- The value of position should be an integer or convertible to an integer, with a range of [-4294967295, 4294967295]. A positive integer indicates searching from the beginning, while a negative integer indicates searching from the end.

    - When the value of position is a NUMBER type with decimals, the function truncates the decimal part and retains the integer part.

    - When the value of position is of floating point type, the function rounds it to the nearest integer.

- When the value of position is 0, the function performs no search and returns 0.

- When position is NULL, the function returns NULL.

**occurrence**

Specifies the position of the occurrence of the substring in expr, which is also optional and defaults to 1. occurrence is a general expression the same as expr, and its value must be numeric or other types that can be converted to NUMBER type.

- The value of occurrence must be a positive integer or convertible to a positive integer, with a range of [1, 4294967295].

    - When the value of occurrence is a NUMBER type with decimals, the function truncates the decimal part and retains the integer part.

    - When the value of occurrence is of floating point type, the function rounds it to the nearest integer.

- When the value of occurrence is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
SELECT INSTRB('abc', 'a') a FROM DUAL;

                    A
---------------------
                    1

SELECT INSTRB('abc', 'b') b FROM DUAL;

                    B
---------------------
                    2
      
SELECT INSTRB('————abc','——',7) c FROM DUAL;

                    C
---------------------
                    7

SELECT INSTRB('————abc','——',7.4) d FROM DUAL;

                    D
---------------------
                    7
```
