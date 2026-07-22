```ebnf
regexp_count = REGEXP_COUNT "(" expr "," regexp ["," position["," match_para]] ")".
```

REGEXP_COUNT is a regex function that counts the number of matches of the target string `regexp` in the source string `[expr](../General SQL Syntax/expr)`, returning the count as a BIGINT type. If no matches are found, the function returns 0.

The function starts matching from the `position`-th character of `expr`, and after the first match, it continues matching from the character immediately following that match, and so on, until the last character of `expr` is reached.

**expr**

A generic expression whose value must be of character type or other types that can be converted to character type.

- `expr` cannot be BFILE or LOB data that exceeds 65534 bytes.

- The function calculates strings using the character set defined in the input (only supports UTF-8).

- When the value of `expr` is NULL, the function returns NULL.

**regexp**

Specifies a [RegExp](../General SQL Syntax/RegExp) with a length not exceeding 512 bytes.

When `regexp` is NULL, the function returns NULL.

**position**

Specifies the offset from which to start matching, which is optional. If omitted, the default value for `position` is 1. `position` is the same generic expression as `expr` and must be a numeric type or convertible to NUMBER type.

- The value of `position` should be a positive integer (0 or negative numbers will cause an error, and decimals will be truncated to the integer part). It is uniformly converted to BIGINT type during internal processing.

- If `position` exceeds the length of `expr`, no match is performed, and the function returns 0.

- When `position` is NULL, the function returns NULL.

<span id="match_para" name="match_para"></span>

**match\_para**

Regex matching parameters, which are optional or can be specified as NULL, with a default value of 'c'. The optional values are as follows:

- 'i': Case insensitive.

- 'c': Default value, case sensitive.

- 'n': Allows the dot (.) to match any character, including newline characters. By default, the dot does not match newline characters.

- 'm': Treats the string as multiline, interpreting ^ and $ as the beginning and end of any line in the string, rather than just the start or end of the entire string. By default, the string is treated as a single line.

- 'x': Ignores whitespace and # comments. By default, whitespace characters match themselves.

If the `match_para` parameter is specified with a value other than the optional ones, the function will return error YAS-04363.

***Example***

```sql
SELECT REGEXP_COUNT('123123123123123', '123', 1, 'i') REGEXP_COUNT FROM DUAL;
         REGEXP_COUNT
---------------------
                    5

SELECT REGEXP_COUNT('123123123123123', '123', 4, 'i') REGEXP_COUNT FROM DUAL; 
         REGEXP_COUNT
---------------------
                    4

SELECT regexp_count('A1B2C3', '[A-Z][0-9]') REGEXP_COUNT FROM DUAL;
         REGEXP_COUNT 
--------------------- 
                    3
```
