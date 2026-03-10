```ebnf+diagram
substring::= SUBSTRING "(" ((expr "," pos ["," len])|(expr FROM pos [FOR len])) ")"
```

The SUBSTRING function is used to extract a substring of a specified length from a source string [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) starting at a specified position.

**expr**

A general expression whose value must be of character type or another type convertible to character type.

- When expr is of LOB type in the vectorized execution engine, the maximum output length cannot exceed 32000 bytes.

- If expr is of CLOB/NCLOB type, the return value will be the same type as expr. If expr is of NCHAR/NVARCHAR type, the return value will be NVARCHAR type; in other cases, the return value will be VARCHAR type.

- If expr is NULL, the function returns NULL.

**pos**

Indicates the position specified by the pos value from which to start extracting the string. pos must be a general expression of the same type as expr and must be a numeric type (other than BIT) or another type convertible to NUMBER, with a range of [-2147483648, 2147483647]. A positive value indicates that the starting position is determined from the beginning of the string, while a negative value indicates that the starting position is determined from the end of the string.

- If pos is of NUMBER type with decimal places (or converted to NUMBER), the function will round it to the nearest integer.

- If pos is of floating-point type, the function will round it based on the odd/even rule.

- If pos is equal to 0 or its absolute value exceeds the length of the string, the function returns NULL.

- If pos is NULL, the function returns NULL.

**len**

Indicates the length of the string to extract as specified by the len value, which can be omitted. len must be a general expression of the same type as expr and must be a numeric type (other than BIT) or another type convertible to NUMBER, with a range of [-2147483648, 2147483647].

- If len is of NUMBER type with decimal places (or converted to NUMBER), the function will round it to the nearest integer.

- If len is of floating-point type, the function will round it based on the odd/even rule.

- If len is not specified, or if len is greater than the length from the specified pos value to the end of the source string, the function returns the substring from the specified pos to the end of the source string.

- If len is 0 or negative, the function returns NULL.

- If len is NULL, the function returns NULL.

***Example***

```sql
SELECT SUBSTRING('abcdefg', 3) a
,SUBSTRING('abcdefg', 3, 2) b
,SUBSTRING('abcdefg', -3) c
,SUBSTRING('abcdefg', 3, -2) d
FROM DUAL;
A         B     C     D
--------- ----- ----- -----
cdefg     cd    efg

SELECT SUBSTRING('abcdefg' FROM 3) a
,SUBSTRING('abcdefg' FROM 3 FOR 2) b
,SUBSTRING('abcdefg' FROM -3) c
,SUBSTRING('abcdefg' FROM 3 FOR -2) d
FROM DUAL;
A         B     C     D
--------- ----- ----- -----
cdefg     cd    efg

SELECT SUBSTRING('abcdefg', CAST(2.5 AS NUMBER), CAST(1.5 AS NUMBER)) a
,SUBSTRING('abcdefg', '2.5', '1.5') b
,SUBSTRING('abcdefg', CAST(2.5 AS FLOAT), CAST(1.5 AS FLOAT)) c
,SUBSTRING('abcdefg', CAST(2.5 AS DOUBLE), CAST(1.5 AS DOUBLE)) d
FROM DUAL;
A         B     C         D
--------- ----- --------- ---------
cd        b     bc        bc

SELECT SUBSTRING('abcdefg' FROM CAST(2.5 AS NUMBER) FOR CAST(1.5 AS NUMBER)) a
,SUBSTRING('abcdefg' FROM '2.5' FOR '1.5') b
,SUBSTRING('abcdefg' FROM CAST(2.5 AS FLOAT) FOR CAST(1.5 AS FLOAT)) c
,SUBSTRING('abcdefg' FROM CAST(2.5 AS DOUBLE) FOR CAST(1.5 AS DOUBLE)) d
FROM DUAL;
A         B     C         D
--------- ----- --------- ---------
cd        b     bc        bc
```
