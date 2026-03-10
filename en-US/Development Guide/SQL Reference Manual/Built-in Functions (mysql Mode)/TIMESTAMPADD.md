```ebnf+diagram
timestampadd::= TIMESTAMPADD "(" unit "," interval ","  datetime_expr ")" 
```

DATEDIFF() adds the integer expression interval to the date or datetime expression datetime_expr.

**unit**

- The unit of the interval is given by the unit parameter, which must be one of the following values: MICROSECOND, SECOND, MINUTE, HOUR, DAY, WEEK, MONTH, QUARTER, or YEAR.
- The unit value can be specified using one of the keywords as shown, or with the SQL_TSI_ prefix. For example, DAY and SQL_TSI_DAY are both valid.

**interval**

- The value corresponding to the unit unit.

**datetime_expr**

- A date or datetime expression.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT TIMESTAMPADD(sql_tsi_year,-1,'2011-1-1');

timestampadd(sql_tsi_year,-1,'2011-1-1')                         
---------------------------------------------------------------- 
2010-01-01                                                      


```
