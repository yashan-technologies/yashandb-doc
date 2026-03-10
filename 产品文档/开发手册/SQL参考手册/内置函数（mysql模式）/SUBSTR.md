```ebnf+diagram
substr::= SUBSTR "(" ((expr "," pos ["," len])|(expr FROM pos [FOR len])) ")"
```
SUBSTR函数为[SUBSTRING](SUBSTRING)函数的同义词。