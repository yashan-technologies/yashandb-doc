```ebnf+diagram
count::= COUNT "(" ("*"|([DISTINCT|ALL] expr)) ")" [OVER "(" analytic_clause ")"]

analytic_clause::= "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")"
```

The COUNT function is consistent with the built-in functions in YashanDB. For usage, please refer to [COUNT](../../../All Manuals/Development Guide/SQL Reference Manual/Built-in Functions/COUNT).