```ebnf+diagram
date_add::= DATE_ADD "(" expr "," INTERVAL interval_value interval_unit ")"
```

The DATE_ADD function is used to perform date calculations, obtaining the result of time advancement or regression by adding the value of [expr](../General SQL Syntax/expr) to the given interval value.

**expr**

A general expression, the value of expr must be of DATE, TIME, TIMESTAMP type, or a string that can be converted to DATE, TIMESTAMP type.

- When expr is a literal, it can only be a string input with DATE and TIMESTAMP keywords, and cannot be a string input with the TIME keyword. For example, DATE '2012-10-12' and TIMESTAMP '2012-10-12 10:20:24.000006' are acceptable expr values for the function, while when expr is TIME '10:20:24', the function returns an error.

- When expr is NULL, the function returns NULL.

**interval_value**

interval_value is a general expression expr that specifies the interval value for time advancement or regression.

**interval_unit**

Specifies the unit of the interval value, which cannot be NULL. The correspondence between unit and corresponding value format is as follows:

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

If the number of digits in the interval_value is less than the number of units in interval_unit, the smaller time unit will be matched first. For example, INTERVAL '1:1' HOUR_SECOND represents one minute and one second, and it is defaulted to assume the hourly unit increment is 0.

**Rules for Date Calculations**

- When expr is of DATE type data, if the minimum unit in interval_unit is one of hours, minutes, seconds, or microseconds, the function returns a TIMESTAMP type; otherwise, the function returns a DATE type.

- When expr is of TIMESTAMP type data, the function returns a TIMESTAMP type.

- When expr is of TIME type data, if interval_unit contains date units, the function returns a TIMESTAMP type; otherwise, the function returns a TIME type.

- When expr is of other types of data, the function returns a VARCHAR type.

***Example*** for  Heap tables

```sql
SELECT DATE_ADD('2012-10-12',INTERVAL  '-1' YEAR) res FROM DUAL;
res                                                              
---------------------------------------------------------------- 
2011-10-12 

SELECT DATE_ADD('2012-10-31',INTERVAL 1 MONTH) res FROM DUAL;
res                                                              
---------------------------------------------------------------- 
2012-11-30
```
