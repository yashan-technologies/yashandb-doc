```ebnf+diagram
subdate::= SUBDATE "(" expr "," (INTERVAL interval_value interval_unit | days) ")"
```

The SUBDATE function is used to perform date calculations, obtaining the result by adding the given interval value to the value of [expr](../General SQL Syntax/expr) for time forward or backward.

**expr**

A general expression, the value of expr must be of DATE, TIME, TIMESTAMP type, or a character type that can be converted to DATE, TIMESTAMP type.

- When expr is a literal, it can only be a string input of the DATE and TIMESTAMP keywords, and cannot be a string input of the TIME keyword. For example, DATE '2012-10-12' and TIMESTAMP '2012-10-12 10:20:24.000006' are acceptable expr values for the function, while expr as TIME '10:20:24' will result in an error from the function.
- When expr is NULL, the function returns NULL.

**interval_value**

interval_value is a general expression expr used to specify the interval value for time advancement or regression.

**interval_unit**

Specifies the unit of the interval value, which cannot be NULL. The format correspondence between the unit and the corresponding value is as follows:

| interval_unit       | interval_format                           |
|---------------------|-------------------------------------------|
| MICROSECOND         | MICROSECONDS                              |
| SECOND              | SECONDS                                   |
| MINUTE              | MINUTES                                   |
| HOUR                | HOURS                                     |
| DAY                 | DAYS                                      |
| WEEK                | WEEKS                                     | 
| MONTH               | MONTHS                                    |
| QUARTER             | QUARTERS                                  |
| YEAR                | YEARS                                     |
| SECOND_MICROSECOND	 | 'SECONDS.MICROSECONDS'                    |
| MINUTE_MICROSECOND  | 'MINUTES:SECONDS.MICROSECONDS'            |
| MINUTE_SECOND       | 'MINUTES:SECONDS'                         |
| HOUR_MICROSECOND    | 'HOURS:MINUTES:SECONDS.MICROSECONDS'      |
| HOUR_SECOND	        | 'HOURS:MINUTES:SECONDS'                   |
| HOUR_MINUTE         | 'HOURS:MINUTES'                           |
| DAY_MICROSECOND     | 'DAYS HOURS:MINUTES:SECONDS.MICROSECONDS' |
| DAY_SECOND          | 'DAYS HOURS:MINUTES:SECONDS'              |
| DAY_MINUTE          | 'DAYS HOURS:MINUTES'                      |
| DAY_HOUR            | 'DAYS HOURS'                              |
| YEAR_MONTH          | 'YEARS-MONTHS'                            |

If the number of digits in interval_value is less than that in interval_unit, the smaller time unit will be prioritized. For example, INTERVAL '1:1' HOUR_SECOND indicates one minute and one second, with the default view of the corresponding increment for the hour unit as 0.

**days**

days is used to specify the integer number of days to be added to expr.

**Date Calculation Rules**

- When expr is of type DATE, if the smallest unit in interval_unit involves seconds, minutes, or microseconds, the function returns a value of TIMESTAMP type; otherwise, it returns a value of DATE type.
- When expr is of type TIMESTAMP, the function returns a value of TIMESTAMP type.
- When expr is of type TIME, the function returns a value of TIME type.
- When expr is of other types, the function returns a value of VARCHAR type.

***Example*** for  Heap tables

```sql
SELECT SUBDATE('2012-10-12',INTERVAL  '-1' YEAR) res FROM DUAL;

res
---------------------------------
2013-10-12

```
