```ebnf+diagram
date_sub::= DATE_SUB "(" expr "," INTERVAL interval_value interval_unit ")"
```

The DATE_SUB function is used to perform date calculations, returning the result of adding the specified interval value to the value of [expr](../General SQL Syntax/expr) to obtain a time advanced or retreated.

**expr**

A general expression, the value of expr must be of DATE, TIME, TIMESTAMP types, or a string that can be converted to DATE or TIMESTAMP types.

- When expr is a literal, it can only be a string input of the DATE and TIMESTAMP keywords, and cannot be a string input of the TIME keyword. For example, DATE '2012-10-12' and TIMESTAMP '2012-10-12 10:20:24.000006' are accepted expr values for the function, whereas when expr is TIME '10:20:24', the function returns an error.
- When expr is NULL, the function returns NULL.

**interval_value**

interval_value is a general expression expr that specifies the interval value to advance or retreat the specified time.

**interval_unit**

Specifies the unit of the interval value, which cannot be NULL; the relationship between the unit and the corresponding value format is as follows:

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

If the number of digits in interval_value is less than the number of digits in interval_unit, the smaller time units will be prioritized for matching. For example, INTERVAL '1:1' HOUR_SECOND represents one minute and one second, with the default hour unit increment considered to be 0.

**Date Calculation Rules**

- When expr is of DATE type data, and the smallest unit in interval_unit involves minutes, seconds, or microseconds, the function returns a TIMESTAMP type; otherwise, it returns a DATE type.
- When expr is of TIMESTAMP type data, the function returns a TIMESTAMP type.
- When expr is of TIME type data, the function returns a TIME type.
- When expr is of other types of data, the function returns a VARCHAR type.

***Example*** for  Heap tables

```sql
SELECT DATE_SUB('2012-10-12',INTERVAL  '-1' YEAR) res FROM DUAL;

res
---------------------------------
2013-10-12

```
