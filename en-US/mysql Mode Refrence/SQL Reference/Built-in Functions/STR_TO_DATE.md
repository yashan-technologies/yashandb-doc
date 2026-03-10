```ebnf+diagram
str_to_date::= STR_TO_DATE "(" str "," format ")" 
```

The STR_TO_DATE function takes a string input and a date format string, returning a result of type DATETIME, DATE, or TIME.

**str**

Represents a date and time string, of VARCHAR type or other types convertible to VARCHAR.

**format**

Specifies a corresponding format string, of VARCHAR type or other types convertible to VARCHAR.

* If format is a constant, the return type is determined by whether the format includes date or time parts, resulting in DATETIME, DATE, or TIME.

* If the format does not contain any date or time format symbols, it returns a DATE type.

* If the format is a variable, it returns a DATETIME type.

* The format supports the following list of formats.

|Format Definition |Return Value |
| -------- | ---------------------------------------------------------- |
| `%a`              | Abbreviated weekday name (Sun..Sat)                          |
| `%b`              | Abbreviated month name (Jan..Dec)                            |
| `%c`              | Month (1...12)                                               |
| `%D`              | Month day with English suffix (0th, 1st, 2nd, 3rd,…)        |
| `%d`              | Day of the month (01..31)                                   |
| `%e`              | Day of the month (1..31)                                    |
| `%f`              | Microseconds (000000..999999)                               |
| `%H`              | Hour in 24-hour format (00...23)                           |
| `%h`              | Hour in 12-hour format (01...12)                           |
| `%I`              | Hour in 12-hour format (01...12)                           |
| `%i`              | Minutes (00...59)                                          |
| `%j`              | Day of the year (001...366)                                |
| `%k`              | Hour in 24-hour format (0...23)                            |
| `%l`              | Hour in 12-hour format (1...12)                            |
| `%M`              | Month name (January...December)                             |
| `%m`              | Month (01...12)                                           |
| `%p`              | AM or PM (AM or PM)                                      |
| `%r`              | 12-hour time with AM or PM (to the second)                 |
| `%S`              | Seconds (00...59)                                        |
| `%s`              | Seconds (00...59)                                        |
| `%T`              | 24-hour time (to the second)                               |
| `%W`              | Weekday name (Sunday...Saturday)                          |
| `%Y`              | Four-digit year                                           |
| `%y`              | Two-digit year                                            |

***Example*** for Standalone Deployment Heap tables

```sql
SELECT STR_TO_DATE('01,5,2025 09:30:17 123456','%d,%m,%Y %h:%i:%s %f') res;

res  
---------------------------------------------------------------- 
2025-05-01 09:30:17.123456

```
