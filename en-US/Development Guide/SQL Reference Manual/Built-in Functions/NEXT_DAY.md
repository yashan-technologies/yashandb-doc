```ebnf
NEXT_DAY = NEXT_DAY "(" expr1 "," expr2 ")" .
```

The NEXT_DAY function calculates the next specified weekday based on the value of expr2, returning a DATE type value for the date represented by expr1.

**expr1**

A [generic expression](../General SQL Syntax/expr) recognized by YashanDB, which must be of DATE/TIMESTAMP/time zone type or a string that can be converted to TIMESTAMP type.

When the value of expr1 is NULL, the function returns NULL.

The function counts from the second day represented by expr1 and obtains the time of the next specified weekday, maintaining the hour, minute, and second of expr1.

**expr2**

A [generic expression](../General SQL Syntax/expr) recognized by YashanDB, which must be numeric or character type. For numeric data that is not an integer, the function will round down.

When the value of expr2 is NULL, the function returns NULL.

For numeric types, the value range for expr2 is `[1,8)`, and values outside this range will result in an error.

For character types, the value of expr2 must be one of the following strings (case insensitive, and the function will ignore trailing spaces):

- Abbreviations of the weekdays: mon, tue, wed, thu, fri, sat, sun.
- Full names of the weekdays: Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday.

***Example***

```sql
-- Return null for null values
SELECT NEXT_DAY(null,null) res FROM DUAL;
RES              
--------------------------------                           

-- Calculate the next Friday starting from the second day after 2022-09-23 07:00:00
SELECT TO_DATE('2022-09-23 07:00:00','yyyy-mm-dd hh24:mi:ss') "cur-year-month-day",
TO_CHAR(TO_DATE('2022-09-23 07:00:00','yyyy-mm-dd hh24:mi:ss'),'yyyy-mm-w-day') "cur-year-month-week-day",
NEXT_DAY(TO_DATE('2022-09-23 07:00:00','yyyy-mm-dd hh24:mi:ss'),6) "year-month-day",
TO_CHAR(NEXT_DAY(TO_DATE('2022-09-23 07:00:00','yyyy-mm-dd hh24:mi:ss'),6),'yyyy-mm-w-day') "year-month-week-day"
FROM DUAL;
cur-year-month-day      cur-year-month-week-day    year-month-day         year-month-week-day   
----------------------- -------------------------- ---------------------- --------------------- 
2022-09-23 07:00:00     2022-09-4-friday           2022-09-30 07:00:00    2022-09-5-friday     

-- Using 'fri' as the parameter
SELECT NEXT_DAY(TO_DATE('2022-09-23 07:00:00','yyyy-mm-dd hh24:mi:ss'),'fri') "year-month-day",
TO_CHAR(NEXT_DAY(TO_DATE('2022-09-23 07:00:00','yyyy-mm-dd hh24:mi:ss'),'fri'),'yyyy-mm-w-day') "year-month-week-day"
FROM DUAL;
year-month-day                   year-month-week-day    
-------------------------------- ----------------------------------
2022-09-30 07:00:00              2022-09-5-friday     

-- Return DATE type result
SELECT NEXT_DAY(TIMESTAMP '2022-09-23 10:00:00.99','friday') "year-month-day",
TO_CHAR(NEXT_DAY(TIMESTAMP '2022-09-23 10:00:00.99','friday'),'yyyy-mm-w-day') "year-month-week-day"
FROM DUAL;
year-month-day                   year-month-week-day       
-------------------------------- ----------------------------
2022-09-30 10:00:00              2022-09-5-friday   
```
