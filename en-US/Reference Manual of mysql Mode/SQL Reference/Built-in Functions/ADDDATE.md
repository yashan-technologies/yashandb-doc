```ebnf
adddate = ADDDATE "(" expr "," (INTERVAL interval_value interval_unit | days) ")".
```

The ADDDATE function is used to perform date calculations by adding the value of [expr](../General SQL Syntax/expr) to a given interval value to obtain results for dates that have been advanced or delayed.

**expr**

This is a general expression, and the value of expr must be of DATE, TIME, TIMESTAMP types, or a string that can be converted to DATE or TIMESTAMP types.

- When expr is a literal, it can only be an input string of the DATE and TIMESTAMP keywords, and cannot be an input string of the TIME keyword. For example, DATE '2012-10-12' and TIMESTAMP '2012-10-12 10:20:24.000006' are acceptable expr values for the function, while expr as TIME '10:20:24' will cause the function to return an error.
- When expr is NULL, the function returns NULL.

**interval_value**

interval_value is a general expression expr that specifies the interval value for advancing or delaying the time.

**interval_unit**

This specifies the unit of the interval value; this value cannot be NULL. The relationship between the unit and the corresponding value format is as follows:

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

If the number of digits in interval_value is less than the number of digits in interval_unit, the smaller time units will have priority in matching. For example, INTERVAL '1:1' HOUR_SECOND indicates one minute and one second, and by default the increment corresponding to the hour unit is regarded as 0.

**days**

days specify the integer number of days to be added to expr.

**Date Calculation Rules**

- When expr is of DATE type data, if the smallest unit referred to in interval_unit involves minutes, seconds, or microseconds, the function returns a TIMESTAMP type; otherwise, the function returns a DATE type.
- When expr is of TIMESTAMP type data, the function returns a TIMESTAMP type.
- When expr is of TIME type data, the function returns a TIME type.
- When expr is of other types, the function returns a VARCHAR type.

***Example*** for Standalone Deployment Heap tables

```sql
SQL> SELECT ADDDATE('2012-10-12',INTERVAL  '-1' YEAR) res FROM DUAL;

res
---------------------------------
2011-10-12

1 row fetched.
```
