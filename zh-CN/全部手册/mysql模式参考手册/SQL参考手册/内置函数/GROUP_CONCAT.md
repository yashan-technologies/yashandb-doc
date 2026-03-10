```ebnf+diagram
group_concat::= GROUP_CONCAT "(" [DISTINCT] (string) {"," (string)} [order_by_clause [SEPARATOR sep_character]] ")"
```

GROUP_CONCAT函数与YashanDB内置函数一致，用法请参考[GROUP_CONCAT](../../../开发手册/SQL参考手册/内置函数/GROUP_CONCAT)。
