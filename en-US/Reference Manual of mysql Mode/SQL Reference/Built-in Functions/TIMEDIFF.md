```ebnf
timediff = TIMEDIFF "(" expr1 "," expr2 ")" .
```

The TIMEDIFF function is used to calculate the difference between expr1 and expr2, returning a value of TIME type.

**expr1/expr2**

- expr1 and expr2 must be general expressions recognized by YashanDB, which must be convertible to TIME type.
- When both expr1 and expr2 are datetime types but not of the same subtype, the function returns NULL.
- When expr1 is NULL, or when expr1 is a valid parameter and expr2 is NULL, the function returns NULL.

***Example*** for  Heap tables

The echo and printing mechanisms for time formats in *yasql* and the MySQL client are different, but this does not affect the business's use of connecting to YashanDB in mysql mode through the JDBC client driver.

The following are query examples via *yasql*.

```sql
-- time type
CREATE TABLE time_time_diff(C1 TIME, C2 TIME);

INSERT INTO time_time_diff VALUES('11:37:10', '10:20:09');

SELECT TIMEDIFF(C1, C2) res FROM time_time_diff;

res                  
-------------------------------- 
01:17:01.000000

-- date type
CREATE TABLE date_date_diff(C1 DATE, C2 DATE);

INSERT INTO date_date_diff VALUES('2022-12-10', '2022-11-24');

SELECT TIMEDIFF(C1, C2) res FROM date_date_diff;

res
--------------------
128:00:00.000000

-- timestamp type
CREATE TABLE timestamp_timestamp_diff(C1 TIMESTAMP, C2 TIMESTAMP);

INSERT INTO timestamp_timestamp_diff VALUES('2022-11-24 11:53:10', '2022-12-10 09:12:49');

SELECT TIMEDIFF(C1, C2) res FROM timestamp_timestamp_diff;

res
--------------------
218:42:10.551616

```

The following are query examples via MySQL Client.

```sql
-- time type
CREATE TABLE time_time_diff(C1 TIME, C2 TIME);

INSERT INTO time_time_diff VALUES('11:37:10', '10:20:09');

SELECT TIMEDIFF(C1, C2) res FROM time_time_diff;
+----------+
| res      |
+----------+
| 01:17:01 |
+----------+

-- date type
CREATE TABLE date_date_diff(C1 DATE, C2 DATE);

INSERT INTO date_date_diff VALUES('2022-12-10', '2022-11-24');

SELECT TIMEDIFF(C1, C2) res FROM date_date_diff;
+------------------+
| res              |
+------------------+
| 384:00:00.000000 |
+------------------+

-- timestamp type
CREATE TABLE timestamp_timestamp_diff(C1 TIMESTAMP, C2 TIMESTAMP);

INSERT INTO timestamp_timestamp_diff VALUES('2022-11-24 11:53:10', '2022-12-10 09:12:49');

SELECT TIMEDIFF(C1, C2) res FROM timestamp_timestamp_diff;
+------------+
| res        |
+------------+
| -381:19:39 |
+------------+

```