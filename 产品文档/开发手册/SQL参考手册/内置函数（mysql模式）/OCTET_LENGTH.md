```ebnf+diagram
octet_length::= OCTET_LENGTH "(" expr ")"
```

OCTET_LENGTH函数统计[expr](../通用SQL语法/expr)的值的长度，返回一个BIGINT的数值。此函数是LENGTH函数的同义词，用法请参考[LENGTH](LENGTH)。