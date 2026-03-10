```ebnf+diagram
to_char::= TO_CHAR "(" expr ["," (format|csid)] ")"
```

The TO_CHAR function converts the value of [expr](../General SQL Syntax/expr) to a CHAR/VARCHAR type string data in the format specified by format.

YashanDB supports converting all ordinary types of data to CHAR/VARCHAR types:

The TO_CHAR function can be divided into the following three categories based on supported types:

* TO_CHAR (datetime type), TO_CHAR (datetime type, FORMAT): When the value of expr is of the datetime type, format specifiers are supported; at this time, the function returns VARCHAR type string data.
* TO_CHAR (numeric type), TO_CHAR (numeric type, FORMAT): When the value of expr is of the numeric type, format specifiers are supported; at this time, the function returns VARCHAR type string data.
* TO_CHAR (other non-date/numeric types):
  * When the value of expr is character type and carries format specifiers, expr attempts to convert to NUMBER type. If the conversion is successful, it executes according to TO_CHAR (numeric type, FORMAT), and if it fails, the function returns a conversion error.
  * If no format specifier is provided, when the value of expr is CHAR type, the function returns CHAR type string data; otherwise, it returns VARCHAR type string data.
* TO_CHAR (BFILE type): When the value of expr is of the BFILE type, it supports carrying character set csid information, and the function returns VARCHAR type string data.

**expr**

A general expression, the data to be converted.

- When the value of expr is NULL, the function returns NULL.
- expr supports implicit conversion for LOB types, but cannot be data of BFILE or LOB types exceeding 65534 bytes.
- When the value of expr exceeds the representational range of NUMBER type, it will return a data overflow error.
- In the vectorized execution engine, expr cannot be out-of-row storage data of LOB types.

**format**

Specify the format to be used for conversion.

The format supports Chinese year, month, and day, which must be enclosed in double quotes, while Chinese characters in expr need not be enclosed in double quotes.

The format for datetime type data supports the following combinations of characters:

* Year
  * Year: 'YYYY', 'YYY', 'YY', 'Y', 'RRRR', 'RR' (where `RRRR` and `RR` apply only to HEAP tables)
  * Year defined by ISO standard: 'IYYY', 'IYY', 'IY', 'I' (applicable only to HEAP tables)
  * Day of the year (1-366): 'DDD'
  * Week of the year (1-53): 'WW'
  * Week of the next year defined by ISO standard (1-53): 'IW' (applicable only to HEAP tables)
  * Century: 'CC'
* Month
  * Month number: 'MM'
  * Full name of the month: 'MONTH'
  * Abbreviation of the month: 'MON'
* Quarter
  * Quarter (1-4): 'Q' (applicable only to HEAP tables)
* Day
  * Day number: 'DD'
* Week
  * Week of the month (1-5): 'W'
  * Full name of the day in a week (SUNDAY-SATURDAY): 'DAY'
  * Day number in a week (1-7): 'D'
* Hour
  * 24-hour format: 'HH24'
  * 12-hour format: 'HH', 'HH12'
  * 12-hour period: 'AM', 'PM', 'A.M.', 'P.M.'
* Minute
  * Number of minutes: 'MI'
* Second
  * Number of seconds: 'SS'
  * Total number of seconds in a day: 'SSSSS' (applicable only to HEAP tables)
  * Decimal places: 'FF', 'FF1'-'FF9', where the number indicates the digit position, and 'FF' defaults to printing 6 decimal places
* Julian Day (vectorization calculation is not supported)
  * Julian Day count: `J`
  * Full English spelling of Julian Day count: `JSP`
* Connectors: ':', '-', '/', '.', ',', ';', '\\', '_', ' ', '[', ']'
* Remove leading/trailing spaces and excessive zeros: `FM`, for example, 'FMYY' (applicable only to HEAP tables)
* Decimal point: 'X', for example, 'XFF'

> **Note**: 
>
> - The signed year format specifier 'SYYYY' has not been implemented. It is currently equivalent to the unsigned year format specifier 'YYYY'.
> - The signed century format specifier 'SCC' has not been implemented. It is currently equivalent to the unsigned century format specifier 'CC'.

The format for numeric type data supports the following combinations of characters:

* Decimal point: `.`, for example, '99.99'
* Dollar sign: `$`, for example, '$9,999'
* Forced zero display: `0`, for example, '00000'
* Specified position returns digit: `9`, for example, '9999'
* Specified position returns decimal point: `D`, for example, '99D99'
* Sets the first or last character as - or +: `S`, for example, 'S9999'
* Thousands separator: `,`, for example, '9,999'
* Return thousands separator at specified position: `G`, for example, '9G9'
* Remove leading/trailing spaces and excessive zeros after decimal: `FM`, for example, 'FM999'

(The following are formats specific to the LSC table)
* When the integer part is zero, return a specified number of spaces for the integer part: `B`, for example, 'B99'
* Show currency symbol at specified position: `C`, for example, 'C99'
* Return the number in scientific notation: `EEEE`, for example, '9EEEE'
* Show currency symbol at specified position: `L`, for example, 'L99'
* Negative values filled with a negative sign at the end, positive values filled with spaces at the end: `MI`, for example, '9MI'
* Negative values enclosed in <> and positive values filled with spaces at the beginning and end: `PR`, for example, '9PR'
* Return the uppercase Roman numeral format: `RN`, for example, 'RN'
* Return the lowercase Roman numeral format: `rn`, for example, 'rn'
* Return the decimal number string with the minimum number of characters: `TM`, for example, 'TM'
* Return Euro or other currency symbols at specified positions: `U`, for example, 'U99'
* Return the value of 10<sup>n</sup>, where n is the number following V: `V`, for example, '99V99'
* Return the hexadecimal value of the specified number: `X`, for example, 'XXX'

When the value of expr is of boolean type data, there is no need to specify the format; doing so will result in an error.

**csid**

When expr is of BFILE type, it specifies the character set ID of the BFILE data. If the character set of BFILE data is consistent with the current character set of the database, the value of csid can be designated as 0 or csid can be completely omitted.


The configurable range for csid is as follows:

|Character Set |csid Value |
|:---------|:------|
| ASCII    | 1     |
| GBK      | 852   |
| UTF-8    | 873   |
| ISO88591 | 31    |
| UTF-16   | 2000  |
| GB18030  | 854   |


***Example***

```sql
SELECT TO_CHAR('Shenzhen') res1,TO_CHAR(True) res2 FROM DUAL;
RES1      RES2  
--------- ----- 
Shenzhen  true 
```

When the value of expr is of datetime type data, not specifying format indicates that the system uses the default format for this date type conversion.

***Example***

```sql
SELECT TO_CHAR(SYSTIMESTAMP+1) res FROM DUAL;
RES                                            
----------------------------------------------------------------
2022-01-10 22:09:27   
 
SELECT TO_CHAR(SYSDATE,'YYYYMMDD HH24:MI:SS') res FROM DUAL;
RES                                            
----------------------------------------------------------------
20220109 22:09:27

SELECT TO_CHAR(SYSDATE, 'YYYY.MM.dd.') RES FROM DUAL;
RES
--------------------------------
2023.11.09.

SELECT TO_CHAR(TIMESTAMP'2024-03-07 20:23:33.123456789', 'YYYYMMDD HH24:MI:SSXFF') XFF FROM dual;

XFF
-------------------------------
20240307 20:23:33.123457
```

When the value of expr is of numeric type data, not specifying format indicates that it is converted to a string in its literal value.

***Example*** for Heap tables
```sql
SELECT TO_CHAR(numbera,'00000') n1,
TO_CHAR(numberb,'99.99') n2,
TO_CHAR(numberc,'$99999999') n3,
TO_CHAR(numberd,'9999999999999999999') n4,
TO_CHAR(numbere,'99D99') n5,
TO_CHAR(numberf) n6,
TO_CHAR(numberg,'9999999999S') n7,
TO_CHAR(numberh) n8
FROM numbers;
N1      N2      N3         N4                    N5        N6                         N7        N8 
------- ------- ---------- --------------------- --------- -------------------------- --------- ----
-00005   55.00    $5555     5555555555555555555    5.55    -5.5555555500000002E+000      555+   1
```

***Example*** for TAC tables and LSC tables
```sql
SELECT TO_CHAR(numbera,'00000') n1,
TO_CHAR(numberb,'99.99') n2,
TO_CHAR(numberc,'$99999999') n3,
TO_CHAR(numberd,'9999999999999999999') n4,
TO_CHAR(numbere,'99D99') n5,
TO_CHAR(numberf) n6,
TO_CHAR(numberg,'9999999999S') n7
FROM numbers_nobit;
N1        N2        N3            N4                    N5        N6                                            N7
--------- --------- ------------- --------------------- --------- --------------------------------------------- -------------
-00005     55.00         $5555     5555555555555555555    5.55    5.5555555555499998E+000                              555+
```

When the value of expr is of character type data, not specifying format indicates that it is converted to a string in its literal value.

***Example*** for Heap tables
```sql
SELECT TO_CHAR(CAST(numbera AS VARCHAR(30)),'00000') n1,
       TO_CHAR(CAST(numberb AS VARCHAR(30)),'99.99') n2,
       TO_CHAR(CAST(numberc AS VARCHAR(30)),'$99999999') n3,
       TO_CHAR(CAST(numberd AS VARCHAR(30)),'9999999999999999999') n4,
       TO_CHAR(CAST(numbere AS VARCHAR(30)),'99D99') n5,
       TO_CHAR(CAST(numberf AS VARCHAR(30))) n6,
       TO_CHAR(CAST(numberg AS VARCHAR(30)),'9999999999S') n7,
       TO_CHAR(CAST(numberh AS VARCHAR(30))) n8
FROM numbers;

N1        N2        N3            N4                    N5        N6                                N7            N8
--------- --------- ------------- --------------------- --------- --------------------------------- ------------- ---------------------------------
-00005     55.00         $5555     5555555555555555555    5.55    -5.5555555500000002E+000                 555+   1
```

***Example*** for TAC tables and LSC tables
```sql
SELECT TO_CHAR(CAST(numbera AS VARCHAR(30)),'00000') n1,
       TO_CHAR(CAST(numberb AS VARCHAR(30)),'99.99') n2,
       TO_CHAR(CAST(numberc AS VARCHAR(30)),'$99999999') n3,
       TO_CHAR(CAST(numberd AS VARCHAR(30)),'9999999999999999999') n4,
       TO_CHAR(CAST(numbere AS VARCHAR(30)),'99D99') n5,
       TO_CHAR(CAST(numberf AS VARCHAR(30))) n6,
       TO_CHAR(CAST(numberg AS VARCHAR(30)),'9999999999S') n7
FROM numbers_nobit;

N1        N2        N3            N4                    N5        N6                                N7
--------- --------- ------------- --------------------- --------- --------------------------------- -------------
-00005     55.00         $5555     5555555555555555555    5.55    5.5555555555499998E+000                  555+
```

When expr's value is of BFILE type data, it supports carrying the character set csid information, indicating conversion to a string after parsing the BFILE file with the corresponding character set.

***Example*** for Heap tables

```sql
CREATE OR REPLACE DIRECTORY TMP_DIR AS '/data/tmp';
!echo "test file ὠ3ὠ4" > '/data/tmp/char.txt';
 SELECT TO_CHAR(BFILENAME('TMP_DIR', 'char.txt')) res FROM DUAL;
RES
----------------------------------------------------------------
test file ὠ3ὠ4

-- Specify utf-8 character set
SELECT TO_CHAR(BFILENAME('TMP_DIR', 'char.txt'), 873) res FROM DUAL;
RES
----------------------------------------------------------------
test file ὠ3ὠ4
```
