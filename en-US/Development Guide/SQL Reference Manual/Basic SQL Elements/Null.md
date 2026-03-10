NULL represents a null value. In YashanDB, an empty string is also treated as NULL. NULL is not used as a definite value, but indicates that the value is in an unknown state or has no meaning.

NULL is only supported in columns that are not defined with NOT NULL or PRIMARY KEY constraints.

## NULL in SQL Built-in Functions

For the handling of parameters given as NULL in YashanDB's SQL built-in functions, please refer to [Built-in Functions](../Built-in Functions (yashan Mode)/00Built-in Functions (yashan Mode)).

## NULL in Comparison Conditions

To check if a value is NULL, use the comparison operator `IS NULL`. If the operand is NULL, the result will return TRUE; otherwise, it will return FALSE.

NULL indicates a null value, meaning missing data, and cannot be compared with other values (including NULL). When using operators like `=` or `!=`, the result will return FALSE.

## NULL in Operations

In YashanDB, the result of any arithmetic or bitwise operation involving NULL will also be NULL. Some comparison, logical, and concatenation operations will return different results based on the given operands, as detailed in the table below:

|Operation |Operator |NULL Involvement |
| -------- | ----------------- | ------------------------------------------------------------ |
| Arithmetic | +                 | Result is NULL                                           |
|           | -                 | Result is NULL                                           |
|           | *                 | Result is NULL                                           |
|           | /                 | Result is NULL                                           |
|           | %                 | Result is NULL                                           |
| Comparison | =                 | Result is FALSE                                          |
|           | != or <>          | Result is FALSE                                          |
|           | >                 | Result is FALSE                                          |
|           | >=                | Result is FALSE                                          |
|           | <                 | Result is FALSE                                          |
|           | <=                | Result is FALSE                                          |
|           | [NOT] IN          | IN:<br />* Left operand is NULL: Result is FALSE<br />* Right set contains NULL: If the set has non-NULL values that equal the left operand, the result is TRUE; otherwise, it is FALSE.<br />NOT IN:<br />* Left operand is NULL: Result is FALSE<br />* Right set contains NULL: If the set contains NULL, the result is FALSE; if the set does not contain NULL and all data is not equal to the left operand, the result is TRUE; otherwise, it is FALSE. |
|           | [NOT] LIKE        | Result is FALSE                                          |
|           | [NOT] BETWEEN AND | Result is FALSE                                          |
|           | IS [NOT] NULL     | IS NULL:<br />* Operand is NULL: Result is TRUE<br />* Operand is not NULL: Result is FALSE<br />IS NOT NULL:<br />* Operand is NULL: Result is FALSE<br />* Operand is not NULL: Result is TRUE |
| Logical   | AND               | Result is NULL                                          |
|           | OR                | * TRUE OR NULL: Result is TRUE<br/>* FALSE OR NULL: Result is NULL<br/>* NULL OR NULL: Result is NULL |
|           | NOT               | Result is NULL                                          |
| Concatenation | &#124;&#124;          | Any data &#124;&#124; NULL: Result is any data                   |
| Bitwise   | &                 | Result is NULL                                          |
|           | &#124;                | Result is NULL                                          |
|           | ^                 | Result is NULL                                          |