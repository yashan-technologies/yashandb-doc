```ebnf+diagram
to_timestamp::= TO_TIMESTAMP "("expr [DEFAULT replace_expr ON CONVERSION ERROR] ["," format] ")"
```

The TO_TIMESTAMP function converts the value of [expr](../General SQL Syntax/expr) to TIMESTAMP type data, returning the value in the default format of TIMESTAMP type.

**expr**

The value of expr must be of character type or time type, and its content must conform to the format; otherwise, a format conversion error will be returned.

When the value of expr is NULL, the function returns NULL.      

**DEFAULT replace_expr ON CONVERSION ERROR**

Indicates that when the conversion of expr fails, the value of replace_expr will be used for conversion. replace_expr is a string literal whose content must conform to the format.

***Example***

```sql
SELECT TO_TIMESTAMP('13-21' DEFAULT '2-13' ON CONVERSION ERROR,'mm-dd') res FROM DUAL;
RES                                            
----------------------------------------------------------------
2022-02-13 00:00:00.000000
```

**format**

format is used to specify the time format of expr. Its value must be of character type and can be omitted; if omitted, the default format for expr is the same as the default format of TIMESTAMP type.

format supports Chinese year, month, and day, and Chinese characters must be enclosed in double quotes when used. The format cannot specify variable-length conversion formats, such as MON, MONTH, DAY; Chinese characters in expr do not need to be enclosed in double quotes.

If the year is not specified in the format, the current year will be obtained and filled in; if the month is not specified, the current month will be obtained and filled in; if the day is not specified, 1 will be used to fill in; if the time is not specified, 0 will be used to fill in.

YashanDB supports matching expr and format without connective characters. The match must follow these rules:

- Numbers in expr will be converted according to the format specified. It is recommended to input numbers of the same length as the conversion format; otherwise, the result may be incorrect.
- The format cannot specify variable-length conversion formats, such as MONTH, DAY.

The specified conversion formats include the following character combinations:

*   Year
    * Year digits: `YYYY`, `Y`, `YY`, `YYY`
*   Month
    * Month digits: `MM`
    * Full month name: `MONTH`
    * Abbreviated month name: `MON`
*   Day
    * Date digits: `DD`
*   Date description
    * Full name of a day of the week (SUNDAY-SATURDAY): `DAY`
    * Numeric day of the week (1-7): `D`
    * Day of the year (1-366): `DDD`
*   Hour
    * 24-hour format: `HH24`
    * 12-hour format: `HH`, `HH12`
*   Minute
    * Minutes: `MI`
*   Second
    * Seconds: `SS`
*   Julian day
    * Julian day count: `J`
*   Connective characters: `:`, `-`, `/`, `.`, `,`, `;`, `\`, `_`, ` `, `[`, `]`
    * `:`, `-`, `/`, `.`, `,`, `;`, `\`, `_`, ` `, `[`, `]`: Connective characters can match each other.
    * ` `: Spaces are not involved in matching.
* Decimal point: 'X'

> **Note**: 
>
> Date description formats must be specified together with year, month, and day formats. The date descriptions in expr must match with year, month, and day; otherwise, an error will be returned.

For the year format, the following conversion rules apply:

| expr\\format | Y   | YY  | YYY | YYYY |
| --- | --- | --- | --- | --- |
| 1   | 2021-06-01 | 2001-06-01 | 2001-06-01 | 0001-06-01 |
| 12  | Error | 2012-06-01 | 2012-06-01 | 0012-06-01 |
| 123 | Error | 0123-06-01 | 2123-06-01 | 0123-06-01 |
| 1234 | Error | 1234-06-01 | Error | 1234-06-01 |

***Example***

```sql
SELECT TO_TIMESTAMP('2','hh') timestamp1,TO_TIMESTAMP(TO_CHAR(SYSDATE)) timestamp2 FROM DUAL;
TIMESTAMP1                   TIMESTAMP2                  
---------------------------- ----------------------------
2022-01-01 02:00:00.000000   2022-01-09 00:00:00.000000

SELECT TO_TIMESTAMP('2020-01-01-02','YYYY-MM-DD-HH') res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2020-01-01 02:00:00.000000                                      

SELECT TO_TIMESTAMP( '2020-01-01,1','YYYY-MM-DD,DDD' ) res FROM DUAL;
RES
-------------------------------- 
2020-01-01 00:00:00.000000                     

-- An error is returned when the date description does not match the input date.
SELECT TO_TIMESTAMP( '2020-01-01,6','YYYY-MM-DD,DDD' ) res FROM DUAL;
[1:22]YAS-00008 type convert error : not a valid day

-- expr without connective characters
SELECT TO_TIMESTAMP('2020010102','YYYY-MM-DD-HH') res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2020-01-01 02:00:00.000000

SELECT TO_TIMESTAMP('2020010102','YYYYMMDDHH') res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2020-01-01 02:00:00.000000

-- expr without connective characters, differing lengths between numbers and conversion format may lead to errors. In this example, 26 is the year, 06 is the month, 21 is the day, and 02 is the hour.
SELECT TO_TIMESTAMP('26062102','YYY-MM-DD-HH') res FROM DUAL;
[1:21]YAS-00008 type convert error : not a valid month

-- expr without connective characters, format cannot specify variable-length conversion formats.
SELECT TO_TIMESTAMP('2020010102','YYYY-MONTH-DD-HH') res FROM DUAL;
[1:21]YAS-00008 type convert error : literal does not match format string

SELECT TO_TIMESTAMP('20602','DDD-HH') res FROM DUAL;
```

**nls_calendar**

Specifies the calendar system used by the database, which only supports string type, with the default value as gregorian.

Currently, YashanDB only supports the gregorian calendar system.

***Example***

```sql
SELECT TO_TIMESTAMP('2020010102','YYYYMMDDHH', 'nls_calendar = gregorian') res FROM DUAL;
RES
----------------------------------------------------------------
2020-01-01 02:00:00.000000
```
