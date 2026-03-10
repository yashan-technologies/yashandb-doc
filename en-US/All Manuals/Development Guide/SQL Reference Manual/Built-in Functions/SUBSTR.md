```ebnf+diagram
substr::= SUBSTR "(" expr "," pos ["," len] ")"
```

The SUBSTR function is used to extract a substring of length len from the source string [expr](../General SQL Syntax/expr) starting at the position pos.

**expr**

A general expression whose value must be character type or other types that can be converted to character type.

- In the vectorized execution engine, expr cannot be LOB type external stored data.

- When the value of expr is of CLOB/NCLOB type, the return value has the same type as the value of expr. When the value of expr is of NCHAR/NVARCHAR type, the return value is of NVARCHAR type; in other cases, the return value is of VARCHAR type.

- When the value of expr is NULL, the function returns NULL.

**pos**

Indicates the position from which to start extracting the string specified by the pos value. pos is a general expression that must be of numeric data type except BIT or other types that can be converted to NUMBER, with a value range of [-2147483648, 2147483647]. A positive value indicates starting position from the beginning of the string, and a negative value indicates starting position from the end of the string.

- When the value of pos is a NUMBER type with decimal places (or converted to NUMBER type), the function truncates the decimal part and keeps the integer part.

- When the value of pos is of float type, the function rounds it using odd-even rounding.

- When the value of pos is 0, it is equivalent to 1.

- When the absolute value of pos exceeds the length of the string, the function returns NULL.

- When the value of pos is NULL, the function returns NULL.

**len**

Indicates the length of the string specified by the len value, which can be omitted. len is a general expression that must be numeric data type except BIT or other types that can be converted to NUMBER, with a value range of [-2147483648, 2147483647].

- When the value of len is a NUMBER type with decimal places (or converted to NUMBER type), the function truncates the decimal part and keeps the integer part.

- When the value of len is of float type, the function rounds it using odd-even rounding.

- When len is not specified, or when the value of len is greater than the length from the position specified by pos to the end of the source string, the function returns a substring starting from the position specified by pos to the end of the source string.

- When the value of len is 0 or negative, the function returns NULL.

- When the value of len is NULL, the function returns NULL.

***Example***

```sql
SELECT SUBSTR('abcdefg', 3) a
,SUBSTR('abcdefg', 3, 2) b
,SUBSTR('abcdefg', -3) c
,SUBSTR('abcdefg', 3, -2) d
FROM DUAL;
A         B     C     D    
--------- ----- ----- -----
cdefg     cd    efg
 
SELECT SUBSTR('abcdefg', CAST(2.5 AS NUMBER), CAST(1.5 AS NUMBER)) a
,SUBSTR('abcdefg', '2.5', '1.5') b
,SUBSTR('abcdefg', CAST(2.5 AS FLOAT), CAST(1.5 AS FLOAT)) c
,SUBSTR('abcdefg', CAST(2.5 AS DOUBLE), CAST(1.5 AS DOUBLE)) d
FROM DUAL;
A         B     C         D
--------- ----- --------- ---------
b         b     bc        bc
```
