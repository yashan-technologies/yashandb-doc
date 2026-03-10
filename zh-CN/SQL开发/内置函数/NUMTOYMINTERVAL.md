```ebnf+diagram
numtoyminterval::= NUMTOYMINTERVAL "(" expr "," "'"(YEAR|MONTH)"'" ")"
```

NUMTOYMINTERVAL函数将[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)表示的除BIT外数值型数据转换为以YEAR|MONTH为单位的INTERVAL YEAR TO MONTH类型的数值。

当expr的值为NULL时，返回NULL。

当expr的值为非数值型数据时，将先进行到NUMBER类型的转换，转换失败时返回Invalid number错误。

当指定YEAR|MONTH单位时，对YEAR|MONTH的大小写不敏感，expr值的取值范围如下：

- years：整数，取值范围为[-178000000,178000000]。

- months：整数，取值范围为[0,11]。

示例

```sql
SELECT NUMTOYMINTERVAL('4','year') res FROM DUAL;
RES
--------------------
+04-00
```
