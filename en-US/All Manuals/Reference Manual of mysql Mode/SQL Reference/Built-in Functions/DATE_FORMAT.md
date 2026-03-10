```ebnf+diagram
DATE_FORMAT::= DATE_FORMAT "(" expr "," format ")" 
```

The DATE_FORMAT function extracts the given parameter [expr](../General SQL Syntax/expr) according to the format specified, returning a VARCHAR type string.

**expr**

The value of expr can be of DATE type or other types that can be converted to DATE type.

* The value of expr is allowed to have month and day set to 0.
* When the value of expr is NULL, the function returns NULL.

**format**

This specifies the date format to be returned, and its value must be a character type or another type convertible to string type.

* When the value of format is NULL, the function returns NULL.
* The format definitions are shown in the table below. If the value of format cannot be matched with the table, the function returns the value of format without any processing.

|Format Definition |Return Value |
| -------- | ---------------------------------------------------------- |
| `%a`             | Abbreviated weekday name (Sun..Sat)                       |
| `%b`             | Abbreviated month name (Jan..Dec)                          |
| `%c`             | Month (1...12)                                            |
| `%D`             | Month date with English suffix (0th, 1st, 2nd, 3rd, ...)  |
| `%d`             | Day of the month (01..31)                                 |
| `%e`             | Day of the month (1..31)                                  |
| `%f`             | Microseconds (000000..999999)                             |
| `%H`             | Hour in 24h format (00...23)                              |
| `%h`             | Hour in 12h format (01...12)                              |
| `%I`             | Hour in 12h format (01...12)                              |
| `%i`             | Minutes (00...59)                                        |
| `%j`             | Day of the year (001...366)                               |
| `%k`             | Hour in 24h format (0...23)                               |
| `%l`             | Hour in 12h format (1...12)                               |
| `%M`             | Month name (January...December)                           |
| `%m`             | Month (01...12)                                          |
| `%p`             | AM or PM (morning or afternoon)                           |
| `%r`             | 12h time with AM or PM (to the second)                    |
| `%S`             | Seconds (00...59)                                        |
| `%s`             | Seconds (00...59)                                        |
| `%T`             | 24h time (to the second)                                   |
| `%U`             | Week number of the year, with Sunday as the first day of the week   |
| `%u`             | Week number of the year, with Monday as the first day of the week    |
| `%V`             | Week number of the year, with Sunday as the first day of the week, usually used with %X |
| `%v`             | Week number of the year, with Monday as the first day of the week, usually used with %x |
| `%W`             | Weekday name (Sunday...Saturday)                          |
| `%w`             | Day of the week (0 for Sunday, 6 for Saturday)            |
| `%X`             | Year to which the week belongs (four digits), with Sunday as the first day of the week, usually used with %V |
| `%x`             | Year to which the week belongs (four digits), with Monday as the first day of the week, usually used with %v |
| `%Y`             | Four-digit year                                          |
| `%y`             | Two-digit year                                           |
| `%%`             | Outputs %                                               |

***Example*** for  Heap tables

```sql
SELECT DATE_FORMAT(NOW(), '%a') res FROM DUAL;
res
----------------------------------------------------------------
Tue

SELECT DATE_FORMAT(NOW(), '%b') res FROM DUAL;
res
----------------------------------------------------------------
Apr

SELECT DATE_FORMAT(NOW(), '%c') res FROM DUAL;
res
----------------------------------------------------------------
4

SELECT DATE_FORMAT(NOW(), '%D') res FROM DUAL;
res
----------------------------------------------------------------
12th

SELECT DATE_FORMAT(NOW(), '%%') res FROM DUAL;
res
----------------------------------------------------------------
%
```
