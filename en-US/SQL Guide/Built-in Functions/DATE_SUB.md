```ebnf+diagram
date_sub::= DATE_SUB "(" expr "," INTERVAL interval_value interval_unit ")"
```

The DATE_SUB function is used to perform date calculations by subtracting the given interval value from the value of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) to obtain the result of time advance or retreat.

**expr**

A generic expression; the value of expr must be of DATE, TIME, TIMESTAMP type or a character type that can be converted to DATE, TIMESTAMP type.

- When expr is a literal, it can only be an input string of DATE and TIMESTAMP keywords, and cannot be a string of the TIME keyword. For example, DATE '2012-10-12' and TIMESTAMP '2012-10-12 10:20:24.000006' are acceptable expr values for the function, while expr set to TIME '10:20:24' will result in an error.

- When expr is NULL, the function returns NULL.

**interval_value**

Specifies the interval value for advancing or retreating in time, and must be in the following forms:

- 0 or positive integer literal, such as 0, 1, 2, etc.
- String literal containing 0 or positive integer content, such as '0', '1', '2', etc.
- String literal containing negative integer content, such as '-1', '-2', etc.
- String literal containing INTERVAL content, such as '10-8', '-8 8:10:24', etc.

**interval_unit**

Specifies the unit of the interval value, which cannot be NULL and must be in the following forms:

- MONTH, YEAR, YEAR TO MONTH keywords (case insensitive): at this time, interval_value is converted to INTERVAL YEAR TO MONTH type.
- SECOND, MINUTE, HOUR, DAY, MINUTE TO SECOND, HOUR TO SECOND, HOUR TO MINUTE, DAY TO SECOND, DAY TO MINUTE, DAY TO HOUR keywords (case insensitive): at this time, interval_value is converted to INTERVAL DAY TO SECOND type.

interval_value and interval_unit must match correctly, for example, 2/YEAR, 3/HOUR, '10-8'/YEAR TO MONTH, '-8 8:10:24'/DAY TO SECOND; otherwise, the function returns a type conversion error.

**Date Calculation Rules**

- When interval_value is of INTERVAL YEAR TO MONTH type, the calculation rules are:

  - First, add or subtract the month, and then check if the days fit the number of days after the month has been added or subtracted.

  - If the number of days after the addition or subtraction is less than the number of days before the addition or subtraction, the number of days after the addition or subtraction equals the last day of the added or subtracted month.

- When the data types of expr and interval_value are not the same, the function first performs type conversion. If the two data types cannot be converted according to certain rules, a type conversion error is returned. The type conversion rules are as follows:

  - If expr is of DATE/TIME/TIMESTAMP type, no conversion is needed, and the function directly returns the data type of expr.

  - If expr is of CHAR/VARCHAR type, the function converts it to DATE type; if the conversion is successful, it returns the converted data type; otherwise, it returns a type conversion error.
  - If expr is TIME and interval_value is of INTERVAL DAY TO SECOND type, exceeding the range will wrap around, i.e., if the resulting hours exceed 24, the remainder will be calculated; for example, if the computed hour is 25, it will wrap around to 1.
  - If expr is TIME and interval_value is of INTERVAL YEAR TO MONTH type, the function returns a type not supported error.

***Example***

```sql
SELECT DATE_SUB('2012-10-12',INTERVAL  '-1' YEAR) res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2013-10-12 00:00:00.000000  

-- interval_value as INTERVAL YEAR TO MONTH type
SELECT DATE_SUB('2012-10-31',INTERVAL 1 MONTH) res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2012-9-30 00:00:00.000000  

-- interval_value as INTERVAL DAY TO SECOND type
SELECT DATE_SUB(CAST('10:20:24' AS TIME),INTERVAL '8:10:24' HOUR TO SECOND) res FROM DUAL;
RES                  
-------------------- 
02:10:00.000000 

-- Hour wrapping
SELECT DATE_SUB(CAST('10:20:24' AS TIME),INTERVAL '14:10:24' HOUR TO SECOND) res FROM DUAL;
RES                  
-------------------- 
20:10:00.000000 
```
