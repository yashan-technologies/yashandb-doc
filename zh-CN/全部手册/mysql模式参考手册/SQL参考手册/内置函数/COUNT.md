```ebnf+diagram
count::= COUNT "(" ("*"|([DISTINCT|ALL] expr)) ")" [OVER "(" analytic_clause ")"]

analytic_clause::= "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")"
```

COUNT函数与YashanDB内置函数一致，用法请参考[COUNT](../../../开发手册/SQL参考手册/内置函数/COUNT)。
