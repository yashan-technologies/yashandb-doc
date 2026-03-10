```ebnf+diagram
to_timestamp_tz::= TO_TIMESTAMP_TZ "("expr [DEFAULT replace_expr ON CONVERSION ERROR] ["," format] ")"
```

The TO_TIMESTAMP_TZ function converts the value of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) to TIMESTAMP WITH TIME ZONE type data.

**expr**

The value of expr must be of character or time type, and its content must comply with the format; otherwise, a format conversion error is returned.

When the value of expr is NULL, the function returns NULL.

**DEFAULT replace_expr ON CONVERSION ERROR**

Indicates that when the conversion of expr fails, the value replace_expr is used for conversion, where replace_expr is a string literal whose content must comply with the format.

***Example***

```sql
SELECT TO_TIMESTAMP_TZ('13-21' DEFAULT '2-13' ON CONVERSION ERROR,'mm-dd') res FROM DUAL;
RES                                            
----------------------------------------------------------------
2022-02-13 00:00:00.000000 +00:00
```

**format**

Format is used to specify the time format of expr, and its value must be character type. It can be omitted; if omitted, the default format of expr will be the same as the default format of TIMESTAMP WITH TIME ZONE type.

Format supports Chinese year, month, and day. When used, Chinese characters must be surrounded by double quotes, and the format cannot specify variable-length conversion formats such as MON, MONTH, DAY; Chinese characters in expr do not need to be surrounded by double quotes.

If the year is not specified in the format, the current year will be obtained and filled in; if the month is not present, the current month will be obtained and filled in; if the day is not present, 1 will be used to fill in; if the time is not specified, 0 will be used to fill in.

YashanDB supports matching expr without connecting characters with format, and this matching must follow the rules below:

- Numbers in expr will be converted according to the format specified. It is recommended to enter numbers and conversion formats of the same length; otherwise, it may lead to incorrect results.
- Format cannot specify variable-length conversion formats, such as MONTH, DAY.

The specified conversion formats include the following character combinations:

*   Year
    * Year numbers: `YYYY`, `Y`, `YY`, `YYY`
*   Month
    * Month numbers: `MM`
    * Full month name: `MONTH`
    * Abbreviated month name: `MON`
*   Day
    * Day numbers: `DD`
*   Date description
    * Full name of a day of the week (SUNDAY-SATURDAY): `DAY`
    * Number of a day of the week (1-7): `D`
    * Day of the year (1-366): `DDD`
*   Hour
    * 24-hour format: `HH24`
    * 12-hour format: `HH`, `HH12`
*   Minute
    * Minute number: `MI`
*   Second
    * Second number: `SS`
*   Julian Day
    * Julian day count: `J`
*   Connecting characters: `:`, `-`, `/`, `.`, `,`, `;`, `\`, `_`, ` `, `[`, `]`
    * `:`, `-`, `/`, `.`, `,`, `;`, `\`, `_`, ` `, `[`, `]`: separators can match with each other
    * ` `: spaces do not participate in matching
* Decimal point: 'X'
* Time zone hour
    * Time zone hour: `TZH`
* Time zone minute
    * Time zone minute: `TZM`

***Example***

```sql
SELECT TO_TIMESTAMP_TZ('2022,JUL:02 08,00.00.0 +8', 'yyyy,MON:dd hh24,mi.ss.ff TZH.TZM') RES FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2022-07-02 08:00:00.000000 +08:00
```

**nls_calendar**

Specifies the calendar system used by the database. It supports only string type, with the default value being gregorian.

Currently, YashanDB supports only the gregorian calendar system.

***Example***

```sql
SELECT TO_TIMESTAMP_TZ('2020010102','YYYYMMDDHH', 'nls_calendar = gregorian') res FROM DUAL;
RES
----------------------------------------------------------------
2020-01-01 02:00:00.000000 +00:00

```
