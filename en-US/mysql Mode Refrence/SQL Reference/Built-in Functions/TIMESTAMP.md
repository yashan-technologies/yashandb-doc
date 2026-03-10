```ebnf+diagram
timestamp::= TIMESTAMP "("timstamp_expr ["," time_expr] ")"
```

If there is 1 parameter, the TIMESTAMP function treats timestamp_expr as TIMESTAMP type and returns the TIMESTAMP value represented by that parameter. The return value is of TIMESTAMP type.

If there are 2 parameters, the TIMESTAMP function treats timestamp_expr as TIMESTAMP type and time_expr as TIME type, returning the sum of the two parameters. The return value is of TIMESTAMP type.

**timestamp_expr**

timestamp_expr must be a [general expression](../General SQL Syntax/expr) recognized by YashanDB and must be convertible to TIMESTAMP type.

When the value of timestamp_expr is character type, its format must meet the following criteria:

*   Character Type 1: The string can be converted to a numeric value and the integer part of the obtained numeric value has at least 5 digits.
*   Character Type 2: A string in 'yyyy-mm-dd' format must comply with the restrictions on year, month, and day, i.e., the year value must be between 0-9999, the month value must be between 1-12, and the day value must be between 1-31. The TIMESTAMP function will pad hours, minutes, and seconds with 0.
*   Character Type 3: A string in 'yyyy-mm-dd hh24:mi:ss.ff' format must comply with the restrictions on year, month, day, hour, minute, and second, i.e., the year value must be between 0-9999, the month value must be between 1-12, the day value must be between 1-31, the hour value must be between 0-23, the minute value must be between 0-59, and the second value must be between 0-59, with microseconds retained up to a maximum of 6 decimal places.

When timestamp_expr is NULL, the function returns NULL.

**time_expr**

time_expr must be a [general expression](../General SQL Syntax/expr) recognized by YashanDB and must be convertible to TIME type.

When time_expr is NULL and timestamp_expr is a valid parameter, the function returns NULL.

***Example*** for  Heap tables

The echo and printing mechanisms for time formats in *yasql* and the MySQL client are different, but this does not affect the business's use of connecting to YashanDB in mysql mode through the JDBC client driver.

The following are query examples via *yasql*.

```sql
SELECT TIMESTAMP('2021-5-31 10:10:10','01:01:01') AS result FROM DUAL;
result
--------------------------------
2021-5-31 11:11:11.000000

SELECT TIMESTAMP('2022-1-30 10:10:10') AS result FROM DUAL;
result
--------------------------------
2022-1-30 10:10:10.000000
```

The following are query examples via MySQL Client.

```sql
SELECT TIMESTAMP('2021-5-31 10:10:10','01:01:01') AS result FROM DUAL;
+---------------------+
| result              |
+---------------------+
| 2021-05-31 11:11:11 |
+---------------------+

SELECT TIMESTAMP('2022-1-30 10:10:10') AS result FROM DUAL;
+---------------------+
| result              |
+---------------------+
| 2022-01-30 10:10:10 |
+---------------------+
```