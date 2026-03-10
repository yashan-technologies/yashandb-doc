```ebnf+diagram
timestamp::= TIMESTAMP "("timstamp_expr ["," time_expr] ")"
```

If there is 1 parameter, the TIMESTAMP function calculates the date represented by timestamp_expr and returns a value of TIMESTAMP type.

If there are 2 parameters, the TIMESTAMP function calculates the date represented by timestamp_expr plus the time represented by time_expr and returns a value of TIMESTAMP type.

**timestamp_expr**

A value recognized by YashanDB as a [universal expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), the value of timestamp_expr must be DATE, TIMESTAMP, TIME, or character data. If it is character data, it must ensure that the string conforms to the current format requirements of the TIMESTAMP type.

When timestamp_expr is NULL, the function returns NULL.

**time_expr**

A value recognized by YashanDB as a [universal expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), the value of time_expr must be DATE, TIMESTAMP, TIME, or character data. If it is character data, it must ensure that the string conforms to the current format requirements of the TIME type.

When time_expr is NULL, the function returns NULL.

***Example***

```sql
SELECT TIMESTAMP('2021-5-31 10:10:10','01:01:01') AS result FROM DUAL;
RESULT
--------------------------------
2021-5-31 11:11:11.000000

SELECT TIMESTAMP('2022-1-30 10:10:10') AS result FROM DUAL;
RESULT
--------------------------------
2022-1-30 10:10:10.000000
```
