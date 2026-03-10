```ebnf+diagram
regexp_instr::= REGEXP_INSTR "(" expr "," regexp ["," position["," occurrence["," return_opt["," match_para["," subexpr]]]]] ")"
```

REGEXP_INSTR is a regular expression function that is used to match the target string regexp in the source string [expr](../General SQL Syntax/expr) according to the regular pattern, returning the return_opt (start or end) position of the subexpr-th substring matched to regexp at the occurrence-th match. The return value is of BIGINT type. If there is no match, the function returns 0.

The function starts matching from the position-th character of expr. After finding the first match, it continues matching from the next character after that match, and so forth until the last character of expr.

This function is similar to the [INSTR](INSTR) function, but this function's target string regexp can use regular expressions.

**expr**

A generic expression whose value must be of character type or another type that can be converted to character type.

- expr cannot be an escape character.

- expr cannot be BFILE or LOB type data exceeding 65534 bytes.

- The function calculates the string based on the character set defined by input (only supports UTF-8).

- When the value of expr is NULL, the function returns NULL.

**regexp**

Specifies a [RegExp](../General SQL Syntax/RegExp) with a length not exceeding 512 bytes.

- regexp supports escape characters.

- When regexp is NULL, the function returns NULL.

**position**

Specifies the offset to start matching. It can be omitted, with a default of 1. position is a generic expression that must be of numeric type or another type that can be converted to NUMBER.

- The value of position should be a positive integer (0 or negative values will result in an error; decimals will be truncated to their integer part), converted internally to BIGINT.

- When position exceeds the length of expr, no matching will be performed, and the function returns 0.

- When position is NULL, the function returns NULL.

**occurrence**

Specifies the position of the occurrence-th match of regexp in expr. It can be omitted, with a default of 1. occurrence is a generic expression that must be of numeric type or another type that can be converted to NUMBER.

- The value of occurrence should be a positive integer (0 or negative values will result in an error; decimals will be truncated to their integer part), converted internally to BIGINT.

- When occurrence exceeds the number of matches, no matching will be performed, and the function returns the value of expr.

- When occurrence is NULL, the function returns NULL.

**return_opt**

Specifies the option for the returned match position, which can only be 0 or 1. It can be omitted, with a default of 0.

- If specified as 0, it will return the position of the first character of the match.

- If specified as 1, it will return the position of the first character after the match.

**match_para**

Regular expression match parameters, which have the same meaning as in [REGEXP_COUNT](REGEXP_COUNT.html#match_para).

**subexpr**

When matching regexp, the function returns the return_opt position of the subexpr-th subexpression that matches regexp. It can be omitted, with a default of 0, indicating the return of the return_opt position of the entire regexp string.

A subexpression refers to expression fragments enclosed in parentheses `()`. Parentheses can be nested, and all subexpressions are numbered according to the order in which their left parentheses appear in the expression, starting from 1 (0 indicates the entire regexp expression). For example, `0123(((abc)(de)f)ghi)45(678)` contains 5 subexpressions, which are "abcdefghi", "abcdef", "abc", "de", and "678" in order.

- The value of subexpr can only be an integer within the range [0,9]. Values less than 0 will result in an error, and values greater than 9 will return 0. Decimals will be truncated to their integer part.

- When the value of subexpr exceeds the number of subexpressions in regexp, the function returns 0.

***Example***

```sql
SELECT REGEXP_INSTR('1234567890', '(123)(4(56)(78))', 1, 1, 0, 'i', 2) "REGEXP_INSTR" FROM DUAL;
         REGEXP_INSTR
---------------------
                    4

SELECT REGEXP_INSTR('1234567890', '(123)(4(56)(78))', 1, 1, 1, 'i', 2) "REGEXP_INSTR" FROM DUAL;

         REGEXP_INSTR
---------------------
                    9

SELECT REGEXP_INSTR('1234567890', '[0-9]', 1, 5) "REGEXP_INSTR" FROM DUAL;
         REGEXP_INSTR
---------------------
                    5      
```
