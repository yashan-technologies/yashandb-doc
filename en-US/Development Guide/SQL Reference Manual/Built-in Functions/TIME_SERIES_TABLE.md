```ebnf
time_series_table = TIME_SERIES_TABLE "(" select_statement ")".
```

TIME_SERIES_TABLE is a built-in table function used to query time series data and return the results in the form of a relational table.

The usage rules are as follows:
- When accessing columns returned by TIME_SERIES_TABLE, column name matching is case-insensitive.
- SQL statements containing this function do not enter the plan cache, and will be reparsed and revalidated on each execution.

**select_statement**

select_statement is a string constant that must be a valid SELECT query statement.

***Examples***

```sql
--Simple Query: Query all data from the time series table test1 where time is greater than 1000
SELECT * FROM TIME_SERIES_TABLE('SELECT * FROM test1 WHERE time > 1000');

--Join Query: Join the temperature data from time series table test1 with the humidity data from time series table test2 by device name
SELECT a.device, a.temperature, b.humidity
FROM TIME_SERIES_TABLE('SELECT device, temperature FROM test1') a
JOIN TIME_SERIES_TABLE('SELECT device, humidity FROM test2') b
ON a.device = b.device;
```