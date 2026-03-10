```ebnf+diagram
timestampdiff::= TIMESTAMPDIFF "(" unit "," expr1 "," expr2 ")" 
```

The TIMESTAMPDIFF function calculates the time difference between expr1 and expr2 based on the time unit specified by unit, returning a BIGINT type value. The return value is positive when expr1 is less than expr2, and negative when expr1 is greater than expr2.

**unit**

Represents the unit of measurement for the function's result. The unit cannot be NULL and must be one of the following character literals:

- MICROSECOND
- SECOND
- MINUTE
- HOUR
- DAY
- WEEK
- MONTH
- QUARTER
- YEAR

**expr1/expr2**

expr1 and expr2 are generic expressions recognized by YashanDB, whose values must be of TIMESTAMP, DATE, or TIME types, or convertible to TIMESTAMP or DATE types from a string.

When the value of expr1 or expr2 is NULL, the function returns NULL.

When the value of expr1 or expr2 is of DATE type, the microsecond part is padded with zeros by the system.

When the value of expr1 or expr2 is of TIME type, the missing date part is filled with the current date value.

***Example*** for  Heap tables

```sql
SELECT TIMESTAMPDIFF(DAY,'1583-10-01 08:00:00','2000-09-30 07:59:59') res FROM DUAL;
                  res 
--------------------- 
               152305

SELECT TIMESTAMPDIFF(DAY,'2022-3-1','2022-4-1') res FROM DUAL;
                  res
---------------------
                   31

SELECT TIMESTAMPDIFF(DAY,'2022-4-1','2022-3-1') res FROM DUAL;
                  res
---------------------
                  -31
```
