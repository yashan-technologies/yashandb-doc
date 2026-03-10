```ebnf+diagram
ascii::= ASCII "(" expr ")"
```

The ASCII function converts a character represented by [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) to its ASCII code and returns an INT value.

This function follows these rules:

*   It only supports conversion of characters with ASCII encoding; other non-ASCII characters (such as Chinese characters) cannot be converted.
*   The value of expr must be of numeric, boolean, datetime, or character type; otherwise, the return type is unsupported.
*   If the value of expr consists of multiple characters, no error will occur; the function will only convert the first character to ASCII and return it.
*   If the value of expr is NULL, the function returns NULL.

***Example***

```sql
--  The function converts the number 23.35 to the string '23.35' and returns the ASCII value of the first character '2'
SELECT ASCII(23.35) result,
TYPEOF(ASCII(23.35)) result_type
FROM DUAL;
      RESULT RESULT_TYPE
------------ -----------
          50 integer
```
