```ebnf
date_add = DATE_ADD "(" expr "," INTERVAL interval_value interval_unit ")".
```

The DATE_ADD function is used to perform date calculations by adding the value of [expr](../General SQL Syntax/expr) to a given interval value, resulting in a time advancement or regression.

**expr**

A general expression; the value of expr must be of DATE, TIME, TIMESTAMP types or convertible to DATE or TIMESTAMP types in character form.

- When expr is a literal, it can only be a string input of DATE and TIMESTAMP keywords, and cannot be a string input of the TIME keyword. For example, DATE '2012-10-12' and TIMESTAMP '2012-10-12 10:20:24.000006' are acceptable expr values for the function, while TIME '10:20:24' will result in an error.
- When expr is NULL, the function returns NULL.

**interval_value**

Specifies the interval value for time advancement or regression and must be in the following forms:

- 0 or positive integer literals, e.g., 0, 1, 2, etc.
- String literals containing 0 or positive integers, e.g., '0', '1', '2', etc.
- String literals containing negative integers, e.g., '-1', '-2', etc.
- String literals containing INTERVAL content, e.g., '10-8', '-8 8:10:24', etc.

**interval_unit**

Specifies the unit of the interval value, which cannot be NULL and must be in the following forms:

- MONTH, YEAR, YEAR TO MONTH keywords (case insensitive): In this case, interval_value is converted to INTERVAL YEAR TO MONTH type.
- SECOND, MINUTE, HOUR, DAY, MINUTE TO SECOND, HOUR TO SECOND, HOUR TO MINUTE, DAY TO SECOND, DAY TO MINUTE, DAY TO HOUR keywords (case insensitive): In this case, interval_value is converted to INTERVAL DAY TO SECOND type.

interval_value and interval_unit must match correctly, such as 2/YEAR, 3/HOUR, '10-8'/YEAR TO MONTH, '-8 8:10:24'/DAY TO SECOND; otherwise, the function returns a type conversion error.

**Date Calculation Rules**

- When interval_value is of INTERVAL YEAR TO MONTH type, the calculation rules are:

  - First, perform the addition or subtraction for months, then check if the day conforms to the number of days in the resultant month.

  - If the resulting month's day count is less than the original month's day count, then the resultant day equals the last day of the resultant month.

- When the data types of expr and interval_value are not the same, the function performs type conversion first. If the two data types cannot be converted according to certain rules, a type conversion error is returned. The type conversion rules are as follows:

  - When expr is of DATE/TIME/TIMESTAMP type, no conversion is required; the function directly returns the data type of expr.

  - When expr is of CHAR/VARCHAR type, the function converts it to DATE type. If conversion is successful, it returns the converted data type; otherwise, it returns a type conversion error.
  - When expr is TIME and interval_value is of INTERVAL DAY TO SECOND type, any overflow will wrap around; for example, if the calculated hours equal 25, it will wrap to 1.
  - When expr is TIME and interval_value is of INTERVAL YEAR TO MONTH type, the function returns a type not supported error. 

***Example***

```sql
SELECT DATE_ADD('2012-10-12',INTERVAL  '-1' YEAR) res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2011-10-12 00:00:00.000000  

-- When interval_value is of INTERVAL YEAR TO MONTH type
SELECT DATE_ADD('2012-10-31',INTERVAL 1 MONTH) res FROM DUAL;
RES                                                              
---------------------------------------------------------------- 
2012-11-30 00:00:00.000000  

-- When interval_value is of INTERVAL DAY TO SECOND type
SELECT DATE_ADD(CAST('10:20:24' AS TIME),INTERVAL '8:10:24' HOUR TO SECOND) res FROM DUAL;
RES                  
-------------------- 
18:30:48.000000 

-- Hour wrap around
SELECT DATE_ADD(CAST('10:20:24' AS TIME),INTERVAL '14:10:24' HOUR TO SECOND) res FROM DUAL;
RES                  
-------------------- 
00:30:48.000000 
```
