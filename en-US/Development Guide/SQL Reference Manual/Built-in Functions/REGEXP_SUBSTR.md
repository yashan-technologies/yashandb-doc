```ebnf
regexp_substr = REGEXP_SUBSTR "(" expr "," regexp ["," position["," occurrence["," match_para["," subexpr]]]] ")".
```

REGEXP_SUBSTR is a regular expression function that matches the target string `regexp` from the source string `[expr](../General SQL Syntax/expr)` based on the regular pattern and returns the `subexpr`-th substring matched for the `occurrence`-th instance of `regexp`. The return value is of VARCHAR type. If no match is found, the function returns NULL.

The function first begins matching from the `position`-th character of `expr`. After matching the first instance, it continues to match from the first character after that match, and so on until reaching the last character of `expr`.

The functionality and parameter definitions of this function are similar to [REGEXP_INSTR](REGEXP_INSTR).

**expr**

The value of `expr` must be of character type or another type that can be converted to character type.

- `expr` cannot be an escape character.

- `expr` cannot be BFILE or LOB type data that exceeds 65534 bytes.

- The function computes the string using the character set defined by the input (supports only UTF-8).

- When the value of `expr` is NULL, the function returns NULL.

**regexp**

Specifies a [RegExp](../General SQL Syntax/RegExp) with a maximum length of 512 bytes.

- `regexp` supports escape characters.

- When `regexp` is NULL, the function returns NULL.

**position**

Specifies the offset where the matching starts, which can be omitted. The default is 1. `position` is a general expression that is the same as `expr` and must be a numerical type or another type that can be converted to NUMBER type.

- The value of `position` should be a positive integer (0 or negative values will cause an error; decimals will be truncated to the integer part). Internally, it is uniformly converted to BIGINT type.

- When `position` exceeds the length of `expr`, no matching will be performed, and the function returns NULL.

- When `position` is NULL, the function returns NULL.

**occurrence**

Specifies the occurrence of the substring matched by `regexp` in `expr`, which can be omitted. The default is 1. `occurrence` is a general expression that is the same as `expr` and must be a numerical type or another type that can be converted to NUMBER type.

- The value of `occurrence` should be a positive integer (0 or negative values will cause an error; decimals will be truncated to the integer part). Internally, it is uniformly converted to BIGINT type.

- When `occurrence` exceeds the number of matches, no matching will be performed, and the function returns NULL.

- When `occurrence` is NULL, the function returns NULL.

**match\_para**

Regular expression match parameters, which have the same meaning as in [REGEXP_COUNT](REGEXP_COUNT.md#match_para).

**subexpr**

When `regexp` is matched, the function returns the `subexpr`-th subexpression (i.e., substring) of `regexp`, which can be omitted. The default is 0, representing the entire `regexp` string.

A subexpression refers to a fragment of expression enclosed in parentheses `()`, and parentheses allow nesting. All subexpressions are numbered in the order in which their left parentheses occur in the expression, starting from 1 (0 indicates the entire `regexp` expression). For example, `0123(((abc)(de)f)ghi)45(678)` contains 5 subexpressions, numbered in order as "abcdefghi", "abcdef", "abc", "de", and "678".

- The value of `subexpr` can only be an integer in the range [0,9]. Values less than 0 will cause an error, values greater than 9 will return NULL, and decimals will be truncated to the integer part.

- When `subexpr` exceeds the number of subexpressions in `regexp`, the function returns NULL.

***Example***

```sql
SELECT REGEXP_SUBSTR('1234567890', '(123)(4(56)(78))', 1, 1,'i', 2) "REGEXP_SUBSTR" FROM DUAL;
REGEXP_SUBSTR
--------------
45678

SELECT REGEXP_SUBSTR('1234567890', '[0-9]', 1, 5) "REGEXP_SUBSTR" FROM DUAL;
REGEXP_SUBSTR
-------------
5
```
