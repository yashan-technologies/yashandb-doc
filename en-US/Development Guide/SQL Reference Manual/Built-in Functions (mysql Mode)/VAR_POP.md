```ebnf+diagram
var_pop::= VAR_POP "(" [ALL] expr ")"
```

The VAR_POP function calculates the population variance of the value of [expr](../General SQL Syntax/expr).

The return type is double.

In single-row calculations, the result of the VAR_POP function is 0.

In multi-row calculations, the system ignores rows with NULL values. When all rows are NULL, the calculation result is NULL.

Aggregate functions cannot be nested, so expr must be a general expression that is not an aggregate function.

**ALL**

The default value, indicating that duplicate data in the expression input is not filtered, and the population variance is calculated directly.

***Example*** for Standalone Deployment Heap tables

```sql
-- Calculate the population variance of employee count; NULL rows will be ignored
SELECT VAR_POP(employee_count) res FROM area1;
 
                 RES
--------------------
          2222.22222
```
