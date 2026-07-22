```ebnf
TIMESTAMPDIFF = TIMESTAMPDIFF "(" unit "," expr1 "," expr2 ")" .
```

The TIMESTAMPDIFF function calculates the time difference between expr1 and expr2 based on the time unit specified by unit, returning a BIGINT type value. The return value is positive when expr1 is less than expr2, and negative when expr1 is greater than expr2.

**unit**

This indicates the unit for the function's result. Unit cannot be NULL and must be one of the following character literals:

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

Expr1 and expr2 are generic expressions recognized by YashanDB, and their values must be of TIMESTAMP, DATE, TIME types or character types that can be converted to TIMESTAMP or DATE types.

When the value of either expr1 or expr2 is NULL, the function returns NULL.

When the value of either expr1 or expr2 is of DATE type, the system fills the microsecond part with zeros.

When the value of either expr1 or expr2 is of TIME type, the system fills the missing date part with the current date value.

***Example***

```sql
SELECT TIMESTAMPDIFF(DAY,'1583-10-01 08:00:00','2000-09-30 07:59:59') res FROM DUAL;
                  RES 
--------------------- 
               152305

SELECT TIMESTAMPDIFF(DAY,'2022-3-1','2022-4-1') res FROM DUAL;
                  RES
---------------------
                   31

SELECT TIMESTAMPDIFF(DAY,'2022-4-1','2022-3-1') res FROM DUAL;
                  RES
---------------------
                  -31
```
