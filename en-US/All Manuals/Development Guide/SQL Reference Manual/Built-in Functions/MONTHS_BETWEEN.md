```ebnf+diagram
MONTHS_BETWEEN::= MONTHS_BETWEEN "(" expr1 "," expr2 ")" 
```

The MONTHS_BETWEEN function is used to calculate the number of full month intervals between expr1 and expr2, along with the fractional part (expr1-expr2), returning a NUMBER type value.

expr1 and expr2 are general expressions recognized by YashanDB, and their values must be of DATE/TIMESTAMP type or convertible to DATE/TIMESTAMP character type.

If either expr1 or expr2 is NULL, the function returns NULL.

***Example***

```sql
-- When the dates are the same, the result is an integer
SELECT MONTHS_BETWEEN('2021-03-13', '2021-02-13') res FROM DUAL;
        RES 
----------- 
          1
               
-- Both are the last day of the month, the result is an integer              
SELECT MONTHS_BETWEEN('2021-04-30', '2021-01-31') res FROM DUAL;
        RES 
----------- 
          3

-- The interval is not a whole month, the result includes decimals               
SELECT MONTHS_BETWEEN('2021-03-01', '2021-02-28') res FROM DUAL;
        RES 
----------- 
 .129032258

-- The interval is not a whole month, the result includes decimals
SELECT MONTHS_BETWEEN('2021-04-01', '2021-02-28') res FROM DUAL;
        RES 
----------- 
 1.12903226
```
