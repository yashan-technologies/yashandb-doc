```ebnf
age = AGE "(" expr1 ["," expr2] ")".
```

The AGE function is used to calculate age and can accept one or two parameters, returning a value of type INTERVAL YEAR TO MONTH.

This function follows the rules below:

- When there is one parameter, the function calculates the time difference by subtracting the parameter value from the current time.
- When there are two parameters, the function calculates the time difference by subtracting the second parameter value from the first parameter value.

Time difference calculation rules:

- Year difference: When the month difference reaches 12 months, it carries over to 1 year.
- Month difference: Compare the months and days of the two values. If the months are equal, the month difference is 0; if the months are not equal (assuming a difference of A), compare the day/hour/minute/second/microsecond:
  - If A is positive, and the minuend is greater than or equal to the subtrahend, the month difference is A; otherwise, it is A-1.
  - If A is negative, and the minuend is less than or equal to the subtrahend, the month difference is A; otherwise, it is A+1.

**expr1, expr2**

expr1 and expr2 are [general expressions](../General SQL Syntax/expr), and their values must be of DATE, TIMESTAMP type, or character types that can be converted to DATE, TIMESTAMP.

- For other types, the function returns a type error message.

- If either expr1 or expr2 is NULL, the function returns NULL.

***Example***

```sql
SELECT AGE('2000-1-1') res FROM DUAL;
RES             
--------------- 
+22-08 

-- When the month difference reaches 12 months, it carries over to 1 year
SELECT AGE('2020-7-31 09:08:00.72','2021-7-31 09:08:00.99') res FROM DUAL;
RES             
--------------- 
-01-00 

-- When the month value difference is negative
SELECT AGE('2020-7-31 09:08:00.72','2021-8-31 09:08:00.66') res FROM DUAL;
RES             
--------------- 
-01-00 

SELECT AGE('2020-7-31 09:08:00.72','2021-8-31 09:08:00.99') res FROM DUAL;
RES             
--------------- 
-01-01   

-- When the month value difference is positive
SELECT AGE('2022-7-31 09:08:00.72','2021-8-31 09:08:00.66') res FROM DUAL;
RES             
--------------- 
+00-11  

SELECT AGE('2022-7-31 09:08:00.72','2021-8-31 09:08:00.99') res FROM DUAL;
RES             
--------------- 
+00-10 
```
