```ebnf
to_date = TO_DATE "("expr [DEFAULT replace_expr ON CONVERSION ERROR] ["," format][","nls_calendar] ")".
```

The TO\_DATE function converts the value of [expr](../General SQL Syntax/expr) to a DATE type data, and the return value is in the default format of DATE type data.

**expr**

The value of expr must be of character type or a type that can be implicitly converted to character type (LOB types support implicit conversion), and its content must conform to the format; otherwise, it will return a format conversion error.

When the value of expr is NULL, the function returns NULL.

For LSC table's LOB type fields, if a row of data is stored outside the row, this function cannot be used.

**DEFAULT replace\_expr ON CONVERSION ERROR**

Indicates that when the conversion of expr fails, use the value of replace\_expr for conversion, where replace\_expr is a string literal that must conform to the format.

***Example***

```sql
SELECT TO_DATE('13-21' DEFAULT '2-13' ON CONVERSION ERROR,'mm-dd') res FROM DUAL;
RES            
--------------------------------
2022-02-13 00:00:00
```

**format**

Format specifies the time format of expr; its value must be of character type and can be omitted. If omitted, it defaults to the same format as the default format of DATE type.

Format supports Chinese year, month, and day. When used, Chinese characters must be enclosed in double quotes, and the format cannot specify a variable-length conversion format, such as MON, MONTH, DAY; Chinese characters in expr do not need to be enclosed in double quotes.

If the year is not specified in the format, the current year will be retrieved and filled; if the month is not specified, the current month will be retrieved and filled; if the day is not specified, it will be filled with 1; unspecified time will be filled with 0.

YashanDB supports matching expr and format without connection characters, and this matching must follow the rules below:

- The numbers in expr will be converted according to the format specified. It is recommended to input numbers and conversion formats of the same length; otherwise, it may lead to incorrect results.
- In the LSC table, the format must be a time format that includes connection characters.
- The format cannot specify a variable-length conversion format, such as MONTH, DAY.

The specified conversion formats include the following character combinations:

* Year
    * Year numbers: `YYYY`, `YYY`, `YY`, `Y`, `RRRR`, `RR` (where `RRRR` and `RR` are only applicable to HEAP tables)
* Month
    * Month numbers: `MM`
    * Full month name: `MONTH`
    * Abbreviated month: `MON`
* Day
    * Day numbers: `DD`
* Date description
    * Full name of a day in the week (SUNDAY-SATURDAY): `DAY`
    * Numerical representation of a day in the week (1-7): `D`
    * Day of the year (1-366): `DDD`
* Hour
    * 24-hour format: `HH24`
    * 12-hour format: `HH`, `HH12`
* Minutes
    * Minute number: `MI`
* Seconds
    * Second number: `SS`
    * Total seconds in a day: `SSSSS` (only applicable to HEAP tables)
* Julian day
  * Julian day count: `J` (only applicable to HEAP tables)
* Connection characters: `:`, `-`, `/`, `.`, `,`, `;`, `\`, `_`, ` `, `[`, `]`
    * `:`, `-`, `/`, `.`, `,`, `;`, `\`, `_`, ` `, `[`, `]`: Connection symbols can match with each other
    * ` `: Spaces do not participate in matching

> **Note**: 
>
> Date description formats must be specified simultaneously with year, month, and day formats, and the date descriptions in expr must match the year and month; otherwise, an error will be returned.
> The signed year format 'SYYYY' has not yet been implemented and is currently equivalent to the unsigned year format 'YYYY'.
> When the range of Julian day counts is within [1,1721057], the corresponding actual year is negative.

The following conversion rules apply to specifying year formats:

| expr\\format | Y   | YY  | YYY | YYYY |
| --- | --- | --- | --- | --- |
| 1   | 2021-06-01 | 2001-06-01 | 2001-06-01 | 0001-06-01 |
| 12  | Error  | 2012-06-01 | 2012-06-01 | 0012-06-01 |
| 123 | Error  | 0123-06-01 | 2123-06-01 | 0123-06-01 |
| 1234 | Error  | 1234-06-01 | Error  | 1234-06-01 |

***Example***

```sql
-- expr contains connection characters
SELECT TO_DATE( 'January 15, 1989, 11:00 A.M.', 'Month dd, YY, HH:MI A.M.' ) res FROM DUAL;
RES                               
--------------------------------
1989-01-15 11:00:00    
 
SELECT TO_DATE( '1989/2/23', 'YYYY/MM/DD HH24:MI:SS' ) res FROM DUAL;
RES                               
--------------------------------
1989-02-23 00:00:00

SELECT TO_DATE( '2020-01-01,1','YYYY-MM-DD,DDD' ) res FROM DUAL;
RES
-------------------------------- 
2020-01-01 00:00:00                    

-- Returns an error when the date description does not match the input date
SELECT TO_DATE( '2020-01-01,6','YYYY-MM-DD,DDD' ) res FROM DUAL;
[1:17]YAS-00008 type convert error : not a valid day

SELECT TO_DATE('0120111-','YYYMMDD-') res FROM DUAL;
RES                              
-------------------------------- 
2012-01-11 00:00:00                      

-- Returns an error when incompatible with the default DATE format 'YYYY-MM-DD'
SELECT TO_DATE( '1989//2/23' ) res FROM DUAL;
[1:16]YAS-00008 type convert error : literal does not match format string

-- expr does not contain connection characters
-- format contains connection characters
SELECT TO_DATE('20120111','YYYY-MM-DD') res FROM DUAL;
RES                              
-------------------------------- 
2012-01-11 00:00:00                      

-- format cannot be a variable-length conversion format
SELECT TO_DATE('2012JANUARY11','YYYY-MONTH-DD') res FROM DUAL;
[1:16]YAS-00008 type convert error : literal does not match format string

-- Numbers will be converted according to the format; in this example, 12 is treated as the year, 01 as the month, and 30 as the date. The conversion format is YYY-MM-DD, thus 120 is recognized as the year, and 13 as the month leading to an error
SELECT TO_DATE('120130','YYY-MM-DD') res FROM DUAL;
[1:16]YAS-00008 type convert error : not a valid month

-- Both expr and format do not contain connection characters
SELECT TO_DATE('20120111','YYYYMMDD') res FROM DUAL;
RES
-------------------------------- 
2012-01-11 00:00:00 

SELECT TO_DATE('2010131','YYYMMDD') res FROM DUAL;
RES
--------------------------------
2201-01-31 00:00:00 

SELECT TO_DATE('2016','YYYMM') res FROM DUAL;
RES                              
-------------------------------- 
2201-06-01 00:00:00 
    
SELECT TO_DATE('2012.01.01.','YYYY.MM.dd.') RES FROM DUAL;

RES
--------------------------------
2012-01-01 00:00:00

SELECT TO_DATE('1721424','J') res FROM DUAL;
RES
-------------------------------- 
0001-01-01 00:00:00
```

**nls_calendar**

Specifies the calendar system used by the database, which must be a string type, with a default value of gregorian. Otherwise, it will return a type error. Currently, YashanDB only supports the calendar system as gregorian; otherwise, it will throw an error.

***Example***

```sql
SELECT TO_DATE( 'January 15, 1989, 11:00 A.M.', 'Month dd, YYYY, HH:MI A.M.', 'nls_calendar = gregorian' ) res FROM DUAL;
RES                                
-------------------------------- 
1989-01-15 11:00:00 
```
