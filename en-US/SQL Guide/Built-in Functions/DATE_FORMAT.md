```ebnf+diagram
date_format::= DATE_FORMAT "(" expr "," format ")" 
```

The DATE_FORMAT function extracts the given parameter [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) according to the format defined by format, returning a VARCHAR type string.
The value of expr can be of DATE type or other types that can be converted to DATE type, while format must be of character type or convertible to string type.

When the value of expr is NULL, the function returns NULL.

**format**

Represents the format corresponding to expr, with the following corresponding list:

|Format Definition |Return Value |
| -------- | ---------------------------------------------------------- |
| `%a`             | Abbreviated weekday name (Sun..Sat)                            |
| `%b`             | Abbreviated month name (Jan..Dec)                              |
| `%c`             | Month (1...12)                                                |
| `%D`             | Month date with English suffix (0th, 1st, 2nd, 3rd,…)         |
| `%d`             | Day of the month (01..31)                                      |
| `%e`             | Day of the month (1..31)                                       |
| `%f`             | Microseconds (000000..999999)                                  |
| `%H`             | Hour in 24-hour format (00...23)                              |
| `%h`             | Hour in 12-hour format (01...12)                              |
| `%I`             | Hour in 12-hour format (01...12)                              |
| `%i`             | Minutes (00...59)                                            |
| `%j`             | Day of the year (001...366)                                   |
| `%k`             | Hour in 24-hour format (0...23)                               |
| `%l`             | Hour in 12-hour format (1...12)                               |
| `%M`             | Month name (January...December)                                |
| `%m`             | Month (01...12)                                              |
| `%p`             | AM or PM (AM or PM)                                          |
| `%r`             | 12-hour time with AM or PM (precise to seconds)                 |
| `%S`             | Seconds (00...59)                                            |
| `%s`             | Seconds (00...59)                                            |
| `%T`             | 24-hour time (precise to seconds)                               |
| `%U`             | Week of the year, with Sunday as the first day of the week    |
| `%u`             | Week of the year, with Monday as the first day of the week     |
| `%V`             | Week of the year, with Sunday as the first day of the week, generally used with %X |
| `%v`             | Week of the year, with Monday as the first day of the week, generally used with %x |
| `%W`             | Weekday name (Sunday...Saturday)                                |
| `%w`             | Day of the week (0 for Sunday, 6 for Saturday)                 |
| `%X`             | The year to which the week belongs (four digits), with Sunday as the first day of the week, generally used with %V |
| `%x`             | The year to which the week belongs (four digits), with Monday as the first day of the week, generally used with %v |
| `%Y`             | Four-digit year                                                |
| `%y`             | Two-digit year                                                 |
| `%%`             | Outputs %                                                     |

If the input format is NULL, the function returns NULL.

If the input format does not match any type in the format list, the corresponding character of the format is output.

***Example***

```sql
SELECT DATE_FORMAT(NOW(), '%a') res FROM DUAL;
RES
----------------------------------------------------------------
Tue

SELECT DATE_FORMAT(NOW(), '%b') res FROM DUAL;
RES
----------------------------------------------------------------
Apr

SELECT DATE_FORMAT(NOW(), '%c') res FROM DUAL;
RES
----------------------------------------------------------------
4

SELECT DATE_FORMAT(NOW(), '%D') res FROM DUAL;
RES
----------------------------------------------------------------
12th

SELECT DATE_FORMAT(NOW(), '%%') res FROM DUAL;
RES
----------------------------------------------------------------
%
```
