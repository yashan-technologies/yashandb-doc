```ebnf
ascii = ASCII "(" expr ")".
```

The ASCII function converts a character represented by [expr](../General SQL Syntax/expr) into its ASCII code, returning an INT type value.

This function follows these rules:

* The value of expr must be of numeric, boolean, datetime, character type, or other types that can be converted to character; otherwise, it returns "type not supported."
* If the value of expr is a multi-character string, no error will be raised; the function will only apply the ASCII conversion to the first character and return that.
* When the value of expr is NULL, the function returns NULL.

***Example*** for  Heap tables

```sql
-- The function converts the numeric value 23.35 into the character '23.35', and returns the ASCII value of the first character '2'
SELECT ASCII(23.35) result,
TYPEOF(ASCII(23.35)) result_type
FROM DUAL;
      result result_type
------------ -----------
          50 integer
```
