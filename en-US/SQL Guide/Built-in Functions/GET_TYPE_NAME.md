```ebnf+diagram
get_type_name::= GET_TYPE_NAME "(" expr ")"
```

The GET_TYPE_NAME function takes [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) as the data type ID to query the name of the type and returns the result as a VARCHAR type string.

This function does not support vectorization calculation.

**expr**

expr must be a numeric type except for BIT, or a character type convertible to INTEGER, with a value range of [0,254]. For other types, the function returns that the type is not supported.

expr cannot be NULL; if the value of expr is NULL, the function will return an error.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT GET_TYPE_NAME(4) TYPE_NAME FROM DUAL;

TYPE_NAME
---------
INTEGER

SELECT GET_TYPE_NAME(5.21) TYPE_NAME FROM DUAL;

TYPE_NAME
---------
BIGINT

SELECT GET_TYPE_NAME(5.71) TYPE_NAME FROM DUAL;

TYPE_NAME
---------
UTINYINT

SELECT GET_TYPE_NAME('26') TYPE_NAME FROM DUAL;

TYPE_NAME
---------
VARCHAR
```
