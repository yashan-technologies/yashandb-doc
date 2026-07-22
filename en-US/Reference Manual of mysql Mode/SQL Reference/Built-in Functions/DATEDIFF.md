```ebnf
datediff = DATEDIFF "(" expr1 "," expr2 ")" .
```

DATEDIFF() returns the number of days from expr1 to expr2. Only the date parts of the values are used in the calculation.

**expr1/expr2**

- expr1/expr2 must be a [general expression](../General SQL Syntax/expr) recognized by YashanDB and must be convertible to DATE or DATETIME type.

***Example*** for  Heap tables

```sql
SQL> select datediff('2021-1-1', '2020-10-30') res;

res
---------------------------------- 
                                63

1 row fetched.

```
