```ebnf+diagram
round::= ROUND "(" ((expr ["," fmt])| (expr ["," round_number])) ")"
```

The ROUND function rounds a date value based on the specified format for the value of [expr](../General SQL Syntax/expr), returning a DATE type date; or rounds according to the specified round_number, returning a type of:

|expr type |Return value |Remarks |
| --- | --- | --- |
| TINYINT | SMALLINT |     |
| SMALLINT | INT |     |
| INT | BIGINT |     |
| BIGINT | NUMBER |     |
| NUMBER | NUMBER |     |
| FLOAT | FLOAT/NUMBER | Outputs FLOAT if round_number is not specified, otherwise outputs NUMBER.|
| DOUBLE | DOUBLE/NUMBER | Outputs DOUBLE if round_number is not specified, otherwise outputs NUMBER. |

When rounding date values, the value of expr must be of type DATE, TIMESTAMP, or a time zone type.

When rounding numeric values, the value of expr must be numeric or a character type that can be converted to NUMBER (conversion failure returns Invalid number error). When the value of expr is 0, the function returns 0.

For other types, the function returns a type conversion error.

When the value of expr is NULL, the function returns NULL.

**fmt**

Specifies the truncation format for the date value, with the following rules:

|Type |Valid format |Return value |Remarks |
| --- |--------------------------------------| --- | --- |
| Century | CC, SCC                               | Rounds to the first day of the next century if greater than 'XX50', otherwise returns the first day of the current century. |  |
| Year | SYYYY, YYYY, YEAR, SYEAR, YYY, YY, Y | Rounds to the first day of the next year starting from July, otherwise returns the first day of the current year. |     |
| ISO Year | IYYY, IY, I                        | Uses the first week of each year as the reference, returns the first day of that week, rounds to the first day of the next year starting from July, otherwise returns the first day of the current year. | The first day of the ISO year may be the last day of December the previous year. |
| Quarter | Q                                   | Rounds to the first day of the next quarter after the 16th day of the second month in each quarter. |     |
| Month | MONTH, MON, MM, RM                    | Rounds to the first day of the next month after the 16th day of each month. |     |
| Week | WW                                    | January 1st of each year is considered the first day of the first week of that year, rounds to the first day of the next week after the 5th day of the week, otherwise returns the first day of the current week. |  |
| IW  | IW                                    | Monday is the first day of each week, rounds to the first day of the next week after the 5th day, otherwise returns the first day of the current week. |     |
| W   | W                                     | The first day of each month is considered the first day of the first week of that month, rounds to the first day of the next week after the 5th day, otherwise returns the first day of the current week. |     |
| Day | DDD, DD, J                           | Returns the day, rounds to the next day at 0:00 from noon `12:00`, otherwise returns 0:00 of that day. |     |
| Start day of the week | DAY, DY, D                | Sunday is the first day of each week, rounds to the first day of the next week after the 5th day, otherwise returns the first day of the current week. |     |
| Hour | HH, HH12, HH24                       | Returns the hour, rounds to the next hour at 0:00 from the 30th minute of each hour, otherwise returns 0:00 of that hour. |     |
| Minute | MI                                 | Returns the minute, rounds to the next minute at 0 seconds from the 30 seconds of each minute, otherwise returns 0 seconds of that minute. |     |
| null | null                                 | null |     |

When fmt is not specified, the default is 'DD'.

For types IW, W, WW, and Start day of the week, when the input value's hour is 12 or greater, rounding may cross days, months, or years.

**round_number**

Specifies the number of decimal places to truncate, which must be numeric data or other types that can be converted to NUMBER.

round_number is a general expression equivalent to expr. When the round_number value is NULL, the function returns NULL.

When the round_number value is a positive number and greater than the decimal places of expr, the function returns the original value of expr.

When the round_number value is a negative number, it indicates truncation to the left of the decimal point. For example, "ROUND(123.456,-2)" returns 123 rounded to the second digit to the left of the decimal point, which is 100. If the absolute value of round_number is greater than the number of digits on the left of the decimal point, the function returns 0, for example, "ROUND(123.456,-4)=0".

  

***Example***

```sql
SELECT ROUND(SYSDATE-10000) res FROM DUAL;
RES             
-------------------------------- 
1995-02-05 

SELECT ROUND(SYSDATE-10000) res FROM DUAL;
RES             
-------------------------------- 
1995-02-05 

SELECT numbera,numberb,numberc,numberd,numbere,numberf,numberg FROM numbers_nobit;
     NUMBERA NUMBERB  NUMBERC               NUMBERD     NUMBERE     NUMBERF     NUMBERG
------------ ------- -------- --------------------- ----------- ----------- -----------
          -5      55     5555   5555555555555555555  5.555E+000  5.556E+000         555
 
SELECT ROUND(numbere,numbera) round1,
ROUND(numbere,numberb) round2,
ROUND(numberf,15660) round3,
ROUND(numbere,'17') round4,
ROUND(numberf,-1) round5
FROM numbers_nobit;
     ROUND1      ROUND2      ROUND3      ROUND4      ROUND5
----------- ----------- ----------- ----------- -----------
          0  5.55499983  5.55555556  5.55499983          10
 
 
SELECT TYPEOF(ROUND(numbere,numbera)) type1,
TYPEOF(ROUND(numbere,numberb)) type2,
TYPEOF(ROUND(numberf,15660)) type3,
TYPEOF(ROUND(numbere,'17')) type4,
TYPEOF(ROUND(numberf,-1)) type5
FROM numbers_nobit;
TYPE1    TYPE2    TYPE3     TYPE4     TYPE5  
-------- -------- --------- --------- --------
number   number   number    number    number
```
