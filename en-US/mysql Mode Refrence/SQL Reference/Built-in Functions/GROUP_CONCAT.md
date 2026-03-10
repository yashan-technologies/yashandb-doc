```ebnf+diagram
group_concat::= GROUP_CONCAT "(" [DISTINCT] (string) {"," (string)} [order_by_clause [SEPARATOR sep_character]] ")"
```

The GROUP_CONCAT function is consistent with the built-in functions in YashanDB. Please refer to [GROUP_CONCAT](../../../All Manuals/Development Guide/SQL Reference Manual/Built-in Functions/GROUP_CONCAT) for usage.