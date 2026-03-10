```ebnf+diagram
time::= TIME "(" expr ")"
```

The TIME function is used to obtain the time portion value of [expr](../General SQL Syntax/expr).

*   When the value of expr is not NULL, the function returns a TIME type.

*   When the value of expr is NULL, the function returns NULL.

**expr**

The value of expr must be of DATE, TIME, TIMESTAMP type or other data types that can be converted to the corresponding time types.

When the value of expr is of character type, its format must comply with the following specifications:

*   String type 1: Starts with 'yyyy-mm-dd' and must contain at least a string in the 'yyyy-mm-dd' format, complying with the general constraints on year, month, and day, such as the month value between 1-12 and the day value between 1-31.

*   String type 2: A string in the 'hh24:mi:ss.ff' format, which can have parts omitted from the end, and must comply with the general constraints on hours, minutes, and seconds, such as the hour value between 0-23 and the minute value between 0-59. In this case, the TIME function will pad the omitted parts with 0.

***Example***

```sql
SELECT TIME(MAX(SYSDATE)) res FROM DUAL;
RES  
--------------------
16:32:15.000000
 
SELECT TIME('2012-3-18') res FROM DUAL;
RES   
--------------------
00:00:00.000000    
 
SELECT TIME('2012-3-18 14') res FROM DUAL;
RES 
--------------------
14:00:00.000000
```
