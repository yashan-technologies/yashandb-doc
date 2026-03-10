```ebnf+diagram
cast::= CAST "(" expr " AS type_name)"
```

The CAST function converts the value of expr to the data type specified by type_name.

**expr**

[General Expression](../General SQL Syntax/expr).

When expr is NULL, the function returns NULL.

When expr is a numeric constant and the target type is character type, regardless of whether the cast is nested, if the length of the data that expr has converted through cast exceeds the defined length of the character type, the function will return an error: data size exceeds limit n.

**type_name**

Specifies the type to convert to. The current type_name supports the following types:

|type_name type |Related Type Behavior |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| BINARY[(N)]         | Produces a string with VARBINARY data type, but when the expression expr is an empty string (zero length), the result type is VARBINARY(0).<br />If an optional length N is provided, BINARY(N) causes the conversion to use parameters no more than N bytes. Values shorter than N bytes are padded with 0x00 bytes to length N.<br />If no optional length N is given, the function calculates the maximum length from the expression. If the provided or calculated length exceeds the internal threshold, the result type is BLOB. |
| CHAR[(N)]           | Produces a string with VARCHAR data type unless the expression expr is an empty string (zero length), in which case the result type is VARCHAR(0).<br />If an optional length N is provided, CHAR(N) causes the conversion to use no more than N character parameters. No padding occurs for values shorter than N characters.<br />If no optional length N is given, the function calculates the maximum length based on the expression. If the provided or calculated length exceeds the internal threshold, the result type is LONGTEXT.   |
| DATETIME[(M)]       | Produces a DATETIME value. If an optional M value is given, it specifies the precision of the fractional seconds.<br />The default precision is 0.<br />When specifying a precision, excess input precision beyond M does not produce an error, and the result is returned with trimmed precision.                                                                                                       |
| DECIMAL[(M[,D])]    | Produces a DECIMAL value. If optional M and D values are given, they specify the maximum number of digits (precision) and the number of decimal places (scale).<br />If D is omitted, it is assumed to be 0. If M is omitted, it is assumed to be 10.<br />                                                                                                   |
| NCHAR[(N)]          | Currently behaves the same as CHAR.                                                                                                                                                                                |
| SIGNED [INTEGER]    | Produces a signed BIGINT value.                                                                                                                                                                            |
| UNSIGNED [INTEGER]  | Produces an unsigned BIGINT UNSIGNED value.                                                                                                                                                                   |
| TIME[(M)]           | Produces a TIME value. If an optional M value is given, it specifies the precision of the fractional seconds.<br />The default precision is 0.<br />When specifying a precision, excess input precision beyond M does not produce an error, and the result is returned with trimmed precision.                                                                                                         |

***Example*** for  Heap tables

```sql
SELECT CAST(12345.12345 AS CHAR) cast FROM dual;
cast
----------------------------------------------------------------
12345.12345

SELECT CAST(12345.12345 AS CHAR(11)) cast FROM dual;
cast
---------------------------------------------
12345.12345

SELECT CAST(12345.12345 AS CHAR(10)) cast FROM dual;
YAS-04425 data size exceeds limit 10

SELECT CAST(321.1234567 AS VARCHAR(8)) FROM DUAL;
[1:28]YAS-04114 need to specify the datatype

SELECT CAST(CAST(321.1234567 AS NUMBER) AS VARCHAR(8)) FROM DUAL;
[1:44]YAS-04114 need to specify the datatype
```
