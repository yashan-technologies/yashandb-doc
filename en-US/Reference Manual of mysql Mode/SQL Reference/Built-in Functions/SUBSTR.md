```ebnf
substr = SUBSTR "(" ((expr "," pos ["," len])|(expr FROM pos [FOR len])) ")".
```
The SUBSTR function is a synonym for the [SUBSTRING](SUBSTRING) function.