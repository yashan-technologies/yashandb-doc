```ebnf+diagram
TIMEDIFF::= TIMEDIFF "(" expr1 "," expr2 ")" 
```

The TIMEDIFF function is used to calculate the time difference between expr1 and expr2, returning a value of type INTERVAL DAY TO SECOND.

**expr1/expr2**

- expr1 and expr2 must be valid [general expressions](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) recognized by YashanDB, and they must be of the same type, which can be TIMESTAMP, DATE, TIME, or a string that can be converted to TIMESTAMP, DATE, or TIME.
- If both expr1 and expr2 are not of string type and they have different types, an error will be raised.
- If one parameter is of type TIMESTAMP, DATE, or TIME, and the other parameter is of string type, the string-type parameter will be converted to the same date and time type as the other parameter.
- If both expr1 and expr2 are string types, both will be converted to TIMESTAMP type.
- If the value of either expr1 or expr2 is NULL, the function returns NULL.

***Example***

```sql
-- time type
CREATE TABLE time_time_diff(C1 TIME, C2 TIME);

INSERT INTO time_time_diff VALUES('11:37:10', '10:20:09');

SELECT TIMEDIFF(C1, C2) res FROM time_time_diff;

RES                  
-------------------------------- 
+00 01:17:01.000000             

-- date type
CREATE TABLE date_date_diff(C1 DATE, C2 DATE);

INSERT INTO date_date_diff VALUES('2022-11-24', '2020-12-10');

SELECT TIMEDIFF(C1, C2) res FROM date_date_diff;

RES                 
-------------------------------- 
+714 00:00:00.000000            

-- timestamp type
CREATE TABLE timestamp_timestamp_diff(C1 TIMESTAMP, C2 TIMESTAMP);

INSERT INTO timestamp_timestamp_diff VALUES('2022-11-24 11:53:10', '2020-12-10 09:12:49');

SELECT TIMEDIFF(C1, C2) res FROM timestamp_timestamp_diff;

RES               
-------------------------------- 
+714 02:40:21.000000

```
