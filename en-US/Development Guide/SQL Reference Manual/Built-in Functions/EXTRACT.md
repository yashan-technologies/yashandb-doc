```ebnf
extract = EXTRACT "(" (YEAR|MONTH|DAY|HOUR|MINUTE|SECOND) FROM expr ")".
```

The EXTRACT function extracts values such as year, month, day, hour, minute, and second from the given parameter [expr](../General SQL Syntax/expr), with the following return value types:

*   When the expr value is NULL, it returns NULL.
*   When the data type of the expr value is not DATE, TIMESTAMP, TIMEZONE, TIME, INTERVAL DAY TO SECOND, or INTERVAL YEAR TO MONTH, the return type does not meet expectations.
*   The data type of the expr value corresponds to the year, month, day, hour, minute, and second as shown in the following table. If N/A appears, it returns an Illegal format error; otherwise, the return type matches the last column:

|Field |TIMESTAMP |TIMEZONE |TIME |DATE |INTERVAL DAY TO SECOND |INTERVAL YEAR TO MONTH |Return Type |
| --- | --- | --- | --- |--- | --- | --- | --- |
| DAY | Day of the month <br>(1-31) | Day of the month <br>(1-31) | N/A | Day of the month <br/>(1-31) | Days  | N/A | INT |
| HOUR | Hour of the day <br/>(0-23) | Hour of the day <br/>(0-23) | Hour of the day <br/> (0-23) | N/A | Hour of the day <br/> (0-23) | N/A | INT |
| MINUTE | Minute of the hour <br/> (0-59) | Minute of the hour <br/> (0-59) | Minute of the hour <br/> (0-59) | N/A | Minute of the hour <br/> (0-59) | N/A | INT |
| MONTH | Month of the year <br/>(1-12) | Month of the year <br/>(1-12) | N/A | Month of the year <br/> (1-12) | N/A | Months  | INT |
| SECOND | Seconds of the minute <br/>(0-59.999999) | Seconds of the minute <br/>(0-59.999999) | Seconds of the minute <br/>(0-59.999999) | N/A | Seconds of the minute <br/>(0-59.999999) | N/A | NUMBER |
| YEAR | Year  | Year  | N/A | Year  | N/A | Years  | INT |

***Example***

```sql
-- Create a table named times that contains columns of types DATE, TIMESTAMP, TIME, INTERVAL DAY TO SECOND, INTERVAL YEAR TO MONTH
CREATE TABLE times (timea DATE DEFAULT SYSDATE,
timeb TIMESTAMP DEFAULT SYSDATE,
timec TIME DEFAULT SYSDATE,
timed INTERVAL DAY TO SECOND,
timee INTERVAL YEAR TO MONTH);
INSERT INTO times VALUES (DEFAULT,DEFAULT,DEFAULT,INTERVAL '5' DAY,INTERVAL '2' YEAR);
COMMIT;
 
SELECT timea,timeb,timec,timed,timee FROM times;
TIMEA                 TIMEB                        TIMEC             TIMED                TIMEE 
--------------------- ---------------------------- ----------------- -------------------- ------
2022-01-17 20:47:19   2022-01-17 20:47:19.000000   20:47:19.000000   +05 00:00:00.000000  +02-00
 
SELECT EXTRACT(YEAR FROM timea) Year,
EXTRACT(SECOND FROM timeb) Second,
EXTRACT(HOUR FROM timec) Hour,
EXTRACT(DAY FROM timed) Day,
EXTRACT(MONTH FROM timee) Month
FROM times;
        YEAR      SECOND         HOUR          DAY        MONTH
------------ ----------- ------------ ------------ ------------
        2022          19           20            5            0
```
