```ebnf+diagram
trunc::= TRUNC "(" ((expr ["," fmt])|(expr ["," n])) ")"
```

The TRUNC function truncates the value of [expr](../General SQL Syntax/expr) of a date value to a specified format, or truncates a numeric value to a specified number of digits, returning a DATE type date value or a NUMBER type numeric value.

**expr**

A general expression whose value must conform to the following data type requirements:

- When used to truncate a date value, the value of expr must be of DATE, TIMESTAMP, or timezone type.

- When used to truncate a numeric value, the value of expr must be a numeric type or another type that can be converted to NUMBER type (conversion failure returns an Invalid number error).

- When the value of expr is NULL, the function returns NULL.

**fmt**

Specifies the truncation format for date value; if not specified, the default is `'DD'`.

The specific rules are as follows:

|Type |Valid Format |Return Value |Remarks |
| --- |--------------------------------------| --- | --- |
| Century | CC, SCC                             | Returns the first day of the century   | The first year of the 21st century is 2001 |
| Year  | SYYYY, YYYY, YEAR, SYEAR, YYY, YY, Y   | Returns the first day of the year      |                                           |
| ISO Year | IYYY, IY, I                        | Uses the week containing January 4 as the first week of the year, returns the first day of that week | ISO first day of the year may be the end of December in the previous year |
| Quarter | Q                                   | Returns the first day of the quarter   |                                           |
| Month | MONTH, MON, MM, RM                     | Returns the first day of the month     |                                           |
| Week  | WW                                   | January 1 of the year as the first day of the first week of the year, returns the first day of that week | Could be any day from Monday to Sunday    |
| IW    | IW                                   | Monday as the first day of the week, returns the first day of that week |                                           |
| W     | W                                    | The 1st of the month as the first day of the first week of the month, returns the first day of that week |                                           |
| Day   | DDD, DD, J                            | Returns the current date                |                                           |
| Start day of the week | DAY, DY, D              | Sunday as the first day of the week, returns the first day of that week |                                           |
| Hour  | HH, HH12, HH24                        | Returns the start time of the current hour |                                         |
| Minute | MI                                   | Returns the start time of the current minute |                                       |
| null | null                                 | null |     |

**n**

Specifies the number of digits to truncate for numeric values. If `n` is not specified, truncation is disabled, and the original data is returned directly.



The specific truncation rules are as follows:

|n Value |Function Behavior |Example |
| --- | --- | --- |
| n is a decimal | Considers the integer part of n for numerical truncation | TRUNC(123.456, 1.6) = TRUNC(123.456, 1) <br/> TRUNC(123.456, -1.2) = TRUNC(123.456, -1) |
| n = 0   | Truncates the decimal part            | TRUNC(123.456, 0) = 123                  |
| n > 0 and within number_expr decimal places | Truncates the part beyond that decimal place | TRUNC(123.456, 2) = 123.45               |
| n > 0 and exceeding number_expr decimal places | Returns the original data                   | TRUNC(123.456, 5) = 123.456               |
| n < 0 and within number_expr integer places | Truncates the decimal part, and sets the integer part to 0 from right to left for n (absolute value) digits | TRUNC(123.456, -1) = 120 <br/> TRUNC(123.456, -2) = 100 |
| n < 0 and exceeding number_expr integer places | Returns 0       | TRUNC(123.456, -5) = 0             |
| n is NULL    | Returns NULL                           |          |



***Example***

```sql
SELECT TRUNC(SYSDATE-10000,'CC') res FROM DUAL;
RES            
--------------------------------
1901-01-01 00:00:00        
 
SET NUMWIDTH 30;
 
SELECT TRUNC('234.33333343',0) "n0",
TRUNC('234.33333343',3) "n3",
TRUNC('234.33333343',30) "n30",
TRUNC('234.33333343',-2) "n-2",
TRUNC('234.33333343',-20) "n-20",
TRUNC('234.33333343',2.98) "n2.98",
TRUNC('234.33333343',-2.98) "n-2.98"
FROM DUAL;
   n0        n3              n30      n-2      n-20        n2.98     n-2.98
----- --------- ---------------- -------- --------- ------------ ----------
  234   234.333     234.33333343      200         0       234.33        200
```
