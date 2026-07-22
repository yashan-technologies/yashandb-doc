```ebnf
var_pop = VAR_POP "(" expr ")".
```

The VAR_POP function calculates the population variance of the value of [expr](../General SQL Syntax/expr).

The return value types of the VAR_POP function are as follows:

*   When the value of expr is TINYINT, SMALLINT, INT, BIGINT, or NUMBER, it returns a NUMBER type.
*   When the value of expr is FLOAT or DOUBLE, it returns the same FLOAT or DOUBLE type as expr.
*   When the value of expr is NULL, it returns NULL.
*   When the value of expr is a literal, variable, or constant, it returns 0.
*   When the value of expr is of another type that cannot be converted to NUMBER, it does not perform the calculation and returns a type conversion error.

In multi-row calculations, the system ignores rows with NULL values. When all rows are NULL, the result is NULL.

***Example***

```sql
-- Calculate the population variance of employee count, empty rows will be ignored
SELECT VAR_POP(employee_count) res FROM area1;
 
                 RES
--------------------
          2222.22222
```
