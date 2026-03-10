```ebnf+diagram
sleep::= SLEEP "(" expr ")"
```

The SLEEP function is used to pause for the duration specified by the parameter, with a pause interval of [expr](../General SQL Syntax/expr) seconds.

When the function is called successfully, it returns 0. If interrupted during the pause, it returns 1 or an error code. If interrupted under the following conditions, the function will return an error code:

- The SLEEP function is present in the WHERE clause.

- The SLEEP function is present in the JOIN clause.

- The SLEEP function is present in the HAVING clause.

**expr**

A generic expression whose value must be numeric or convertible to a numeric type.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT SLEEP(1.5);

           sleep(1.5)
---------------------
                    0


SELECT SLEEP('true');

[1:14]YAS-00008 type convert error : not a valid number
```
