```ebnf+diagram
substrb::= SUBSTRB "(" expr "," pos ["," len] ")"
```

The SUBSTRB function is used to extract a substring of length len from the source string [expr](../General SQL Syntax/expr) starting at the position pos.

This function does not support vectorization calculation.

**expr**

A general expression whose value must be of character type or other types that can be converted to character type, excluding NCLOB type.

- expr cannot be LOB type data exceeding 65534 bytes.

- When expr is of RAW type, the return value is of RAW type; when expr is of NCHAR/NVARCHAR type, the return value is of NVARCHAR type; in other cases, the return value is of VARCHAR type.

- When expr is NULL, the function returns NULL.

**pos**

Indicates the position to start extracting the string as specified by the pos value. pos must be a general expression of a numeric type, excluding BIT, or another type that can be converted to NUMBER, with a value range of [-2147483648, 2147483647]. A positive value indicates that the starting position is determined from the front, while a negative value indicates that it is determined from the back.

- When pos is a NUMBER type with decimal points (or converted to NUMBER type), the function truncates the decimal part and retains the integer part.

- When pos is of floating-point type, the function uses round half to even rounding.

- When pos is NULL, the function returns NULL.

- When pos is 0, it is equivalent to 1.

- When the absolute value of pos exceeds the length of the string, the function returns NULL.

**len**

Indicates the length of the string to be extracted as specified by the len value and can be omitted. len must be a general expression of a numeric type, excluding BIT, or another type that can be converted to NUMBER, with a value range of [-2147483648, 2147483647].

- When len is a NUMBER type with decimal points (or converted to NUMBER type), the function truncates the decimal part and retains the integer part.

- When len is of floating-point type, the function uses round half to even rounding.

- When len is not specified or when len's value is greater than the length from the position specified by pos to the end of the source string, the function returns the substring from the position specified by pos to the end of the source string.

- When len is 0 or a negative number, the function returns NULL.

- When len is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
SELECT SUBSTRB('——abc', 4) a1
,SUBSTRB('Aabc', 4) a2
,SUBSTRB('——abc', 3, 4) b1
,SUBSTRB('Aabc', 3, 4) b2
,SUBSTRB('——abc', -3) c1
,SUBSTRB('Aabc', -3) c2
,SUBSTRB('——abc', 3, -4) d1
,SUBSTRB('Aabc', 3, -4) d2
FROM DUAL;
A1        A2    B1    B2    C1    C2    D1    D2
--------- ----- ----- ----- ----- ----- ----- -----
—abc      c      —    bc    abc   abc
 
SELECT SUBSTRB('abcdefg', CAST(2.5 AS NUMBER), CAST(1.5 AS NUMBER)) a
,SUBSTRB('abcdefg', '2.5', '1.5') b
,SUBSTRB('abcdefg', CAST(2.5 AS FLOAT), CAST(1.5 AS FLOAT)) c
,SUBSTRB('abcdefg', CAST(2.5 AS DOUBLE), CAST(1.5 AS DOUBLE)) d
FROM DUAL;
A         B     C         D
--------- ----- --------- ---------
b         b     bc        bc
```
