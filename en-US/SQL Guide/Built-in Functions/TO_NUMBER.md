```ebnf+diagram
to_number::= TO_NUMBER "("expr ["," format] ")"
```

The TO_NUMBER function converts the value of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) to NUMBER type data according to the format.

The data type of the expr value can be:

*   Numeric: In this case, the format does not need to be specified, and the function converts it directly to NUMBER type.
*   Character: In this case, the function converts it according to the format, and the content of expr must conform to the specified format.

When the value of expr is NULL, the function returns NULL.

**format**

Specifies the conversion format, supporting the following combinations of characters:

*   Decimal point: `.`, for example, '99.99'
*   Dollar sign: `$`, for example, '$9,999'
*   Force zero display: `0`, for example, '00000'
*   Positioning numbers: `9`, for example, '9999'
*   Positioning decimal points: `D`, for example, '99D99'
*   Specify the leading or trailing as - or +: `S`, for example, 'S9999'
*   Thousand separator: `,`, for example, '9,999'
*   Positioning thousand separators: `G`, for example, '9G9'
*   Trim leading/trailing spaces and extra zeros after the decimal: `FM`, for example, 'FM999'

(The following are specific formats for LSC table)
*   Return space for integer part when the integer part is zero: `B`, for example, 'B99'
*   Positioning currency symbol: `C`, for example, 'C99'
*   Return numbers in scientific notation: `EEEE`, for example, '9EEEE'
*   Display currency symbol at specified position: `L`, for example, 'L99'
*   Fill negative values with negative sign and positive values with spaces at the end: `MI`, for example, '9MI'
*   Enclose negative values in <> and fill positive values with spaces at both ends: `PR`, for example, '9PR'
*   Return uppercase Roman numeral format: `RN`, for example, 'RN'
*   Return lowercase Roman numeral format: `rn`, for example, 'RN'
*   Return decimal numeric string with minimum characters: `TM`, for example, 'TM'
*   Return euro and other currency symbols at specified position: `U`, for example, 'U99'
*   Return 10<sup>n</sup> value, where n is the number following V: `V`, for example, '99V99'
*   Return hexadecimal value of the specified number: `X`, for example, 'XXX'

***Example***

```sql
-- Convert all data to NUMBER type output; the display width of this data is related to SET NUMWIDTH
SELECT TO_NUMBER(numbera) n1,
TO_NUMBER(numberb) n2,
TO_NUMBER(numberc) n3,
TO_NUMBER(numberd) n4,
TO_NUMBER(numbere) n5,
TO_NUMBER(numberf) n6,
TO_NUMBER(numberg) n7
FROM numbers;
        N1          N2          N3          N4          N5          N6          N7       
----------- ----------- ----------- ----------- ----------- ----------- ----------- 
         -5          55        5555  5.5556E+18  5.55499983  -5.5555556         555       
```
