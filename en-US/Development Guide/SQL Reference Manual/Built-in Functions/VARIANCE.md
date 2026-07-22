```ebnf
variance = VARIANCE "(" [DISTINCT|UNIQUE|ALL] expr ")" [keep_clause].

keep_clause = KEEP "(" DENSE_RANK (FIRST|LAST) order_by_clause ")".
```

The VARIANCE function calculates the sample variance of [expr](../General SQL Syntax/expr).

When there is only one row of data provided as an argument, the result of the VARIANCE function is 0.

The return value types of the VARIANCE function are as follows:

* When the value of expr is of type TINYINT, SMALLINT, INT, BIGINT, or NUMBER, it returns a NUMBER type.
* When the value of expr is of type FLOAT or DOUBLE, it returns a FLOAT or DOUBLE type consistent with the value of expr.
* When the value of expr is NULL, it returns NULL.
* When the value of expr is a literal, variable, or constant, it returns 0.
* When the value of expr is of another type that cannot be converted to NUMBER, the calculation is not performed and a type conversion error is returned.

**DISTINCT|UNIQUE**

Indicates that the sample variance calculation is performed after filtering out duplicate input data.

DISTINCT and UNIQUE are completely equivalent.

**ALL**

The default value, indicating that duplicate input data for the expression is not filtered and the sample variance is calculated directly.

***Example***

```sql
-- Calculate the sample variance of employee count, empty rows will be ignored, this statement is equivalent to SELECT VARIANCE(ALL employee_count) FROM area1
SELECT VARIANCE(employee_count) res FROM area1;
                 RES
--------------------
          3333.33333
 
-- The sample variance for only one record is 0
SELECT VARIANCE(employee_count) res FROM area1 where area_no='02';
                 RES
--------------------
                   0

-- Calculate the sample variance after removing duplicate employee counts
SELECT VARIANCE(DISTINCT employee_count) res FROM area1;
                 RES
--------------------
                5000
```

**keep_clause**

The syntax for the KEEP aggregate function is generic; for details refer to [KEEP aggregate function](00Built-in Functions.md#KeepAggregateFunction).

When using the KEEP clause, the VARIANCE function cannot use DISTINCT.

***Example***

```sql
-- The finance_info table records income by year, month, and branch
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- Calculate the sample variance of salaries for the earliest year
SELECT VARIANCE(revenue_total) KEEP (DENSE_RANK FIRST ORDER BY YEAR) FROM finance_info;

VARIANCE(REVENUE_TOTAL)KEEP(DENSE_RANKFIRSTORDERBYYEAR)
-------------------------------------------------------
                                                      0

-- Calculate the sample variance of salaries for the latest year
SELECT VARIANCE(revenue_total) KEEP (DENSE_RANK LAST ORDER BY YEAR) FROM finance_info;

VARIANCE(REVENUE_TOTAL)KEEP(DENSE_RANKLASTORDERBYYEAR)
------------------------------------------------------
                                            21207552.9
```
