```ebnf+diagram
variance::= VARIANCE "(" [ALL] expr ")"
```

The VARIANCE function calculates the population variance of the value of [expr](../General SQL Syntax/expr) and is synonymous with [VAR_POP](VAR_POP).

The return type is double.

In a single-row calculation, the result of the VARIANCE function is 0.

In a multi-row calculation, the system ignores rows with null values; if all rows are null, the result is NULL.

Aggregate functions cannot be nested, so expr must be a general expression other than aggregate functions.

The BLOB and TEXT type are not allowed to calculate with this function.

**ALL**

By default, it indicates that duplicate data in the expression input is not filtered, and the population variance is calculated directly.

***Example*** for Standalone Deployment Heap tables

```sql
-- Calculate the population variance of the number of employees; empty rows will be ignored
SELECT VARIANCE(employee_count) res FROM area1;
 
                 res
--------------------
          2222.22222
```
