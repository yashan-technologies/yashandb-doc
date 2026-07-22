```ebnf
var_samp = VAR_SAMP "(" expr ")".
```

The VAR_SAMP function calculates the sample variance of the value of [expr](../General SQL Syntax/expr).

When the given parameter contains only one row of data, the result of the VARIANCE function is NULL.

The return types of the VAR_SAMP function are as follows:

*   When the value of expr is TINYINT, SMALLINT, INT, BIGINT, or NUMBER types, it returns a NUMBER type.
*   When the value of expr is FLOAT or DOUBLE type, it returns the same FLOAT or DOUBLE type as the value of expr.
*   When the value of expr is NULL, it returns NULL.
*   When the value of expr is a literal, variable, or constant, it returns 0.
*   When the value of expr is of another type that cannot be converted to a NUMBER type, the calculation is not performed, and a type conversion error is returned.

In multi-row calculations, the system ignores rows with NULL values. When all rows have NULL values, the result of the calculation is NULL.

***Example***

```sql
-- Calculate the sample variance of the employee count, rows with NULL will be ignored
SELECT VAR_SAMP(employee_count) res FROM area1;
                 RES
--------------------
          3333.33333
 
-- The sample variance of a single record is NULL
SELECT VAR_SAMP(employee_count) res FROM area1 where area_no='02';
                 RES
--------------------
                    
```
