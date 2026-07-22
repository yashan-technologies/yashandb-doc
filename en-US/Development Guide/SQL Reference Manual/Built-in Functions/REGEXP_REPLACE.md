```ebnf
regexp_replace = REGEXP_REPLACE "(" expr "," regexp ["," replace["," position["," occurrence["," match_para]]]] ")".
```

REGEXP_REPLACE is a regular expression function used to match the target string `regexp` in the source string `[expr](../General SQL Syntax/expr)` according to the regular pattern, and replaces the `occurrence`-th occurrence of the matched `regexp` substring with the `replace` string, finally returning the modified complete string, with a return value of type VARCHAR. If no match is found, the function returns the source string `expr`.

The function first starts matching from the `position`-th character of `expr`, continuing to match from the first character following the match until the last character of `expr` is reached.

This function is similar to the `[REPLACE](REPLACE)` function, but the target string `regexp` of this function can use regular expressions.

**expr**

A generic expression, which must be of character type or another type that can be converted to character type.

- `expr` cannot be a BFILE or LOB type data exceeding 65534 bytes.

- The function computes the string using the character set defined by the input (only supports UTF-8).

- An error will be reported if the length of the result string exceeds the limit (65534).

- When the value of `expr` is NULL, the function returns NULL.

**regexp**

Specifies a `[RegExp](../General SQL Syntax/RegExp)`, with a length not exceeding 512 bytes.

When `regexp` is NULL, the function returns `expr`.

**replace**

The string to be used for replacement, `replace` must be a generic expression that is character type or can be converted to character type.

**position**

Specifies the offset to start matching, can be omitted, defaulting to 1. `position` must be a generic expression of the same type as `expr`, and must be numeric or convertible to NUMBER type.

- The value of `position` should be a positive integer (0 or negative will result in an error, decimals will be truncated to the integer part), and is uniformly converted to BIGINT internally.

- If `position` exceeds the length of `expr`, no match is performed and the function returns the value of `expr`.

- If `position` is NULL, the function returns NULL.

**occurrence**

Specifies the occurrence number for replacing the substring matched by `regexp` in `expr`, can be omitted, defaulting to 1. `occurrence` must be a generic expression of the same type as `expr`, and must be numeric or convertible to NUMBER type.

- The value of `occurrence` should be 0 or a positive integer (negative numbers will result in an error, and decimals will be truncated to the integer part), and is uniformly converted to BIGINT internally.

- If `occurrence` is 0, all matches will be replaced.

- If `occurrence` exceeds the number of matches found, no match is performed and the function returns the value of `expr`.

- If `occurrence` is NULL, the function returns NULL.

**match\_para**

Regular matching parameters, with the same meaning as in `[REGEXP_COUNT](REGEXP_COUNT.md#match_para)`.

***Example***

```sql
SELECT REGEXP_REPLACE('1234567890', '456','!', 1, 1,'i') "REGEXP_REPLACE" FROM DUAL;
REGEXP_REPLACE
--------------
123!7890

SELECT REGEXP_REPLACE('1234567890', '[0-9]','!', 1, 5) "REGEXP_REPLACE" FROM DUAL;
REGEXP_REPLACE
--------------
1234!67890

SELECT REGEXP_REPLACE(DHQ,'h','H',1,0,'i') "REGEXP_REPLACE"
FROM area
WHERE area_no='01';
REGEXP_REPLACE                                                   
------------------ 
SHangHai  
```
