```ebnf+diagram
time::= TIME "(" expr ")"
```

The TIME function extracts the time portion value of [expr](../General SQL Syntax/expr), and the return value of the function is of TIME type.

When the value of expr is numeric, its value range is -8385959.999999 to -8385959.999999. For any numeric value, the TIME function takes the lowest 2 digits of the integer part as seconds, the middle 2 digits as minutes, and the highest 3 digits as hours. The decimal part retains at most 6 digits, and if the number of digits is insufficient, it will be padded with 0. The hours, minutes, and seconds obtained from the number must meet the restriction conditions, that is, the hour value is between 0-838, the minute value is between 0-59, the second value is between 0-59, and the microsecond value is between 0-999999.

When the value of expr is of character type, its format must comply with the following specifications:

*   Character type 1: The string can be converted to a numeric value. In this case, it is equivalent to the processing of numeric values by the TIME function.
*   Character type 2: A string in 'yyyy-mm-dd hh24:mi:ss.ff' format must comply with the restrictions on years, months, days, hours, minutes, and seconds, that is, the year value is between 0-9999, the month value is between 1-12, the day value is between 1-31, the hour value is between 0-23, the minute value is between 0-59, and the second value is between 0-59. In this case, the TIME function extracts only the content corresponding to 'hh24:mi:ss.ff' format. The substring corresponding to 'hh24:mi:ss.ff' can omit parts from the right, the string can have a negative sign at the front, but the result returned by the function does not include the negative sign.
*   Character type 3: A string in 'hh838:mi:ss.ff' format, which can omit parts from the right and must comply with the restrictions on minutes and seconds, that is, the minute value is between 0-59 and the second value is between 0-59. In this case, the hour value is between 0-838, the separator can only use `':'`, the string can have a negative sign at the front, and the function returns the result including the negative sign.

***Example*** for  Heap tables

The echo and printing mechanisms for time formats in *yasql* and the MySQL client are different, but this does not affect the business's use of connecting to YashanDB in mysql mode through the JDBC client driver.

The following are query examples via *yasql*.

```sql

SELECT TIME('-13:24:56.66') res FROM DUAL;

res
--------------------
74:36:52.891616

SELECT TIME(8385959) res FROM DUAL;

res
--------------------
70:59:59.000000

SELECT TIME('2012-3-18') res FROM DUAL;
[1:13]YAS-00008 type convert error : incorrect time value

SELECT TIME('2012-3-18 14') res FROM DUAL;

res
--------------------
14:00:00.000000

```

The following are query examples via MySQL Client.

```sql

SELECT TIME('-13:24:56.66') res FROM DUAL;
+--------------+
| res          |
+--------------+
| -13:24:56.66 |
+--------------+

SELECT TIME(8385959) res FROM DUAL;
+-----------+
| res       |
+-----------+
| 838:59:59 |
+-----------+

SELECT TIME('2012-3-18') res FROM DUAL;
ERROR 8 (HY000): YAS-00008 type convert error : incorrect time value

SELECT TIME('2012-3-18 14') res FROM DUAL;
+----------+
| res      |
+----------+
| 14:00:00 |
+----------+

```