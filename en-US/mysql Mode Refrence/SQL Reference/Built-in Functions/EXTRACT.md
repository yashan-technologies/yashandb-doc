```ebnf+diagram
extract::= EXTRACT "(" (YEAR|QUARTER|MONTH|WEEK|DAY|HOUR|MINUTE|SECOND|MICROSECOND|SECOND_MICROSECOND|MINUTE_MICROSECOND|MINUTE_SECOND|HOUR_MICROSECOND|HOUR_SECOND|HOUR_MINUTE|DAY_MICROSECOND|DAY_SECOND|DAY_MINUTE|DAY_HOUR|YEAR_MONTH) FROM expr ")"
```

The EXTRACT function extracts values such as year, month, day, hour, minute, second, etc., from the given parameter [expr](../General SQL Syntax/expr).

**expr**

The value of expr must be of type DATE, DATETIME, TIMESTAMP, TIME, or other types that can be converted to time types.

*   When expr is NULL, NULL is returned.

*   When expr is of type TIME, the current system time will fill in year, month, and day.

*   When expr is of type DATE, hours, minutes, and seconds will be filled with 0.

*   When expr is of type DATETIME or TIMESTAMP, the input year, month, day, hour, minute, and second information will be used directly; missing values will be filled with 0.

*   When expr is not a time type:

    *   If the extraction unit is YEAR, QUARTER, MONTH, WEEK, DAY, or YEAR_MONTH, expr will be converted to TIMESTAMP type for processing.

    *   If the extraction unit is HOUR, MINUTE, SECOND, MICROSECOND, SECOND_MICROSECOND, MINUTE_MICROSECOND, MINUTE_SECOND, HOUR_MICROSECOND, HOUR_SECOND, or HOUR_MINUTE, expr will be converted to TIME for processing.

    *   If the extraction unit is DAY_MICROSECOND, DAY_SECOND, DAY_MINUTE, or DAY_HOUR:

        * When expr is a string, if the valid digits of expr are 6 or fewer, it will be converted to TIME for processing; otherwise, it will be converted to TIMESTAMP for processing. If the string contains only one decimal point, the number before the decimal point is considered valid digits.

        * When expr is of numeric type, if it has 6 digits or fewer, expr will be converted to TIME for processing; otherwise, it will be converted to TIMESTAMP for processing.

        * When expr is of other types, it will be converted to TIMESTAMP for processing.

***Example*** for Standalone Deployment Heap tables

```sql
-- Create table times with columns of types DATE, TIMESTAMP, TIME
CREATE TABLE times (timea DATE DEFAULT SYSDATE,
timeb TIMESTAMP DEFAULT SYSDATE,
timec TIME DEFAULT SYSDATE);
INSERT INTO times VALUES (DEFAULT,DEFAULT,DEFAULT);
COMMIT;

-- example via yasql
SELECT timea,timeb,timec FROM times;
timea                            timeb                                                            timec
-------------------------------- ---------------------------------------------------------------- --------------------
2025-03-18 14:54:19              2025-03-18 14:54:19.000000                                       14:54:19.000000

-- example via MySQL Client
SELECT timea,timeb,timec FROM times;
+------------+---------------------+----------+
| timea      | timeb               | timec    |
+------------+---------------------+----------+
| 2025-10-16 | 2025-10-16 14:08:33 | 14:08:33 |
+------------+---------------------+----------+

SELECT timea,timeb,timec FROM times;
+------------+---------------------+----------+
| timea      | timeb               | timec    |
+------------+---------------------+----------+
| 2025-10-16 | 2025-10-16 14:08:33 | 14:08:33 |
+------------+---------------------+----------+
 
SELECT EXTRACT(YEAR FROM timea) Year,
EXTRACT(MONTH FROM timeb) Month,
EXTRACT(DAY FROM timec) Day,
EXTRACT(HOUR FROM timea) Hour,
EXTRACT(SECOND FROM timeb) Second
FROM times;
                Year                 Month                   Day                  Hour                 Second
--------------------- --------------------- --------------------- --------------------- ---------------------
                 2025                     3                    18                    14                    19

SELECT EXTRACT(YEAR FROM 123) Year,
       EXTRACT(MONTH FROM 123) Month,
       EXTRACT(DAY FROM 123) Day,
       EXTRACT(HOUR FROM 123) Hour,
       EXTRACT(SECOND FROM 123) Second
FROM DUAL;
                Year                 Month                   Day                  Hour                 Second
--------------------- --------------------- --------------------- --------------------- ---------------------
                 2000                     1                    23                     0                    23
```
