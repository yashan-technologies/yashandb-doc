```ebnf+diagram
DAYOFWEEK::= DAYOFWEEK "(" expr ")" 
```

The DAYOFWEEK function is used to calculate the day of the week for the [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) based on the week, with Sunday as the first day, returning an INT type value.

This function does not support vectorization calculation.

The value of expr must be of TIMESTAMP/DATE type or convertible to TIMESTAMP/DATE type string.

When the value of expr is NULL, the function returns NULL.

> **Note**: 
>
> YashanDB uses the Gregorian calendar starting from the extrapolated date; it does not align with the Oracle calendar prior to October 15, 1582 (exclusive).

***Example*** for Heap tables

```sql
SELECT DAYOFWEEK(DATE '2022-7-26') res FROM DUAL;
         RES 
------------ 
           3

-- When microseconds exceed 6 digits, rounding is applied
SELECT DAYOFWEEK('2022-10-28 23:59:59.999999999') res FROM DUAL;
         RES 
------------ 
           7
           
SELECT DAYOFWEEK('2022-10-28 23:59:59.99999901') res FROM DUAL;
         RES 
------------ 
           6
```
