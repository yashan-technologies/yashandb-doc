```ebnf
to_nchar = TO_NCHAR "(" expr ["," (format|csid)] ")".
```

The TO_NCHAR function converts the value of [expr](../General SQL Syntax/expr) to an NCHAR/NVARCHAR string according to the specified format.

YashanDB supports converting all common data types to NCHAR/NVARCHAR:

TO_NCHAR can be divided into the following three categories based on supported types:

* TO_NCHAR (datetime), TO_NCHAR (datetime, FORMAT): When expr is a datetime type, the format specifier is supported; the function returns NVARCHAR string data.
* TO_NCHAR (numeric), TO_NCHAR (numeric, FORMAT): When expr is a numeric type, the format specifier is supported; the function returns NVARCHAR string data.
* TO_NCHAR (non-datetime/numeric types):
  * When expr is a character type with a format specifier, expr is first attempted to convert to NUMBER type; if successful, TO_NCHAR (numeric, FORMAT) is executed, otherwise an conversion error is returned.
  * When no format specifier is provided, if expr is NCHAR type, the function returns NCHAR string data; otherwise it returns NVARCHAR string data.
* TO_NCHAR (BFILE): When expr is BFILE type, the character set csid information is supported, and the function returns NVARCHAR string data.

**expr**

A general expression representing the data to be converted.

- When expr is NULL, the function returns NULL.
- expr supports implicit conversion of LOB types, but BFILE and LOB data exceeding 65534 bytes are not supported.
- When expr exceeds the representable range of NUMBER type, an overflow error is returned.
- In vectorized execution engine, expr cannot be out-of-row stored LOB data.

**format**

Specifies the conversion format.

The format supports Chinese year/month/day characters, which must be enclosed in double quotes. Chinese characters in expr do not need to be enclosed in double quotes.

Datetime data supports the following character format combinations:

* Year
  * Year value: 'YYYY', 'YYY', 'YY', 'Y', 'RRRR', 'RR' (where `RRRR` and `RR` are only applicable to HEAP tables)
  * Year defined by ISO standard: 'IYYY', 'IYY', 'IY', 'I' (only applicable to HEAP tables)
  * Day of year (1-366): 'DDD'
  * Week of year (1-53): 'WW'
  * Week of year by ISO standard (1-53): 'IW' (only applicable to HEAP tables)
  * Century: 'CC'
* Month
  * Month number: 'MM'
  * Full month name: 'MONTH'
  * Abbreviated month name: 'MON'
* Quarter
  * Quarter (1-4): 'Q' (only applicable to HEAP tables)
* Day
  * Day of month: 'DD'
* Week
  * Week of month (1-5): 'W'
  * Full name of day of week (SUNDAY-SATURDAY): 'DAY'
  * Day of week number (1-7): 'D'
* Hour
  * 24-hour format: 'HH24'
  * 12-hour format: 'HH', 'HH12'
  * 12-hour AM/PM indicator: 'AM', 'PM', 'A.M.', 'P.M.'
* Minute
  * Minutes: 'MI'
* Second
  * Seconds: 'SS'
  * Total seconds of day: 'SSSSS' (only applicable to HEAP tables)
  * Fractional seconds: 'FF', 'FF1'-'FF9', where the number specifies the digit count, 'FF' defaults to 6 decimal places
* Julian Day (vectorized computation not supported)
  * Julian day count: `J`
  * Julian day count in full English: `JSP`
* Separator characters: ':', '-', '/', '.', ',', ';', '\', '_', ' ', '[', ']'
* Remove leading/trailing spaces and excess zeros: `FM` (only applicable to HEAP tables)
* Decimal point: 'X', for example 'XFF'

> **Note**:
>
> - The signed year format 'SYYYY' is not yet implemented and currently behaves the same as the unsigned year format 'YYYY'.
> - The signed century format 'SCC' is not yet implemented and currently behaves the same as the unsigned century format 'CC'.

Numeric data supports the following character format combinations:

*   Decimal point: `.`, for example '99.99'
*   Dollar sign: `$`, for example '$9,999'
*   Force zero display: `0`, for example '00000'
*   Specify position to return digit: `9`, for example '9999'
*   Specify position to return decimal point: `D`, for example '99D99'
*   Prefix or suffix with - or +: `S`, for example 'S9999'
*   Thousands separator: `,`, for example '9,999'
*   Return thousands separator at specified position: `G`, for example '9G9'
*   Remove leading/trailing spaces and trailing zeros after decimal: `FM`, for example 'FM999'

When expr is Boolean type, the format should not be specified; specifying format will cause an error.

**csid**

When expr is BFILE type, specifies the character set ID of the BFILE data. If the character set of BFILE data is the same as the current database character set, csid can be set to 0 or omitted entirely.


The configurable csid values are as follows:

| Character Set | csid Value |
|:-------------|:-----------|
| ASCII        | 1          |
| GBK          | 852        |
| UTF-8        | 873        |
| ISO88591     | 31         |
| UTF-16       | 2000       |
| GB18030      | 854        |


Examples

```sql
SELECT TO_NCHAR('Shenzhen') res1,TO_NCHAR(True) res2 FROM DUAL;
RES1      RES2
--------- -----
Shenzhen  true
```

When expr is datetime type data, not specifying format means the system converts using the default format for that date type.

Examples

```sql
SELECT TO_NCHAR(SYSTIMESTAMP+1) res FROM DUAL;
RES
----------------------------------------------------------------
2022-01-10 22:09:27

SELECT TO_NCHAR(SYSDATE,'YYYYMMDD HH24:MI:SS') res FROM DUAL;
RES
----------------------------------------------------------------
20220109 22:09:27

SELECT TO_NCHAR(SYSDATE,'YYYY.MM.dd.') RES FROM DUAL;
RES
--------------------------------
2026.06.12.

select to_nchar(TIMESTAMP'2024-03-07 20:23:33.123456789', 'YYYYMMDD HH24:MI:SSXFF') XFF from dual;

XFF
-------------------------------
20240307 20:23:33.123457
```

When expr is numeric type data, not specifying format means the data is converted to string based on its literal value.

Examples (HEAP table)
```sql
SELECT TO_NCHAR(numbera,'00000') n1,
TO_NCHAR(numberb,'99.99') n2,
TO_NCHAR(numberc,'$99999999') n3,
TO_NCHAR(numberd,'9999999999999999999') n4,
TO_NCHAR(numbere,'99D99') n5,
TO_NCHAR(numberf) n6,
TO_NCHAR(numberg,'9999999999S') n7,
TO_NCHAR(numberh) n8
FROM numbers;
N1      N2      N3         N4                    N5        N6                         N7        N8
------- ------- ---------- --------------------- --------- -------------------------- --------- ----
-00005   55.00    $5555     5555555555555555555    5.55    -5.5555555500000002E+000      555+   1
```

When expr is character type data, not specifying format means the data is converted to string based on its literal value.

Examples (HEAP table)
```sql
SELECT TO_NCHAR(cast(numbera as nvarchar(30)),'00000') n1,
       TO_NCHAR(cast(numberb as nvarchar(30)),'99.99') n2,
       TO_NCHAR(cast(numberc as nvarchar(30)),'$99999999') n3,
       TO_NCHAR(cast(numberd as nvarchar(30)),'9999999999999999999') n4,
       TO_NCHAR(cast(numbere as nvarchar(30)),'99D99') n5,
       TO_NCHAR(cast(numberf as nvarchar(30))) n6,
       TO_NCHAR(cast(numberg as nvarchar(30)),'9999999999S') n7,
       TO_NCHAR(cast(numberh as nvarchar(30))) n8
FROM numbers;

N1        N2        N3            N4                    N5        N6                                N7            N8
--------- --------- ------------- --------------------- --------- --------------------------------- ------------- ---------------------------------
-00005     55.00         $5555     5555555555555555555    5.55    -5.5555555500000002E+000                 555+   1
```

When expr is BFILE type data, the csid character set information is supported, indicating that the BFILE file is parsed according to the corresponding character set and then converted to string.

Examples (HEAP table)

```sql
CREATE OR REPLACE DIRECTORY TMP_DIR AS '/data/tmp';
!echo "test file ὠ3ὠ4" > '/data/tmp/char.txt';
SELECT TO_NCHAR(bfilename('TMP_DIR', 'char.txt')) res FROM DUAL;
RES
----------------------------------------------------------------
test file ὠ3ὠ4

-- Specify UTF-8 character set
SELECT TO_NCHAR(bfilename('TMP_DIR', 'char.txt'), 873) res FROM DUAL;
RES
----------------------------------------------------------------
test file ὠ3ὠ4
```
