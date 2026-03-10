```ebnf+diagram
var_samp::= VAR_SAMP "(" [ALL] expr ")"
```

The VAR_SAMP function calculates the sample variance of [expr](../General SQL Syntax/expr).

The return types of the VAR_SAMP function are as follows:

The return type is double.

In a single-row calculation, the result of the VAR_SAMP function is 0.

In a multi-row calculation, the system ignores rows with NULL values, and if all rows are NULL, the result is NULL.

Aggregate functions cannot be nested, so expr must be a general expression other than aggregate functions.

**ALL**

The default value, indicating that duplicate data from the expression input is not filtered, and the sample standard deviation is calculated directly.

***Example*** for  Heap tables

```sql
-- Calculate the sample variance of employee count; NULL rows will be ignored
SELECT VAR_SAMP(employee_count) res FROM area1;
                 res
--------------------
          3333.33333
 
-- The sample variance with only one row of records is NULL
SELECT VAR_SAMP(employee_count) res FROM area1 WHERE area_no='02';
                 res
--------------------
                    
```
