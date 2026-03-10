```ebnf+diagram
nls_lower::= NLS_LOWER "(" expr ["," expr ] ")"
```

NLS_LOWER函数根据指定的排序规则将[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)的值中的大写字母转换为小写，当第二个参数缺省时，NLS_LOWER等同于LOWER。

本函数不支持向量化计算。

**初始数据expr**

待转换的初始数据，通用表达式，其值须为字符型或可转换为字符型的其他类型。

- expr不能为超过65534字节的字符型数据。

- 当expr的值为NULL时，函数返回NULL。

**字符序expr**

通过第2个expr可指定字符序，格式为`"NLS_SORT=collation"`，可省略，默认为`"NLS_SORT=CHINESE_PINYIN"`。目前仅支持将如下字符序对应的数据转换小写：

- CHINESE_PINYIN：区分大小写的拼音排序。

- CHINESE_PINYIN_CI：不区分大小写的拼音排序。

- BINARY：二进制编码排序。

**返回值类型**

- 当初始数据expr为字符类型时，返回值为与之对应的变长字符类型。

- 当初始数据expr为CLOB/NCLOB类型时，返回值为初始数据类型一致。

- 其余场景返回值均为VARCHAR类型。

示例（单机HEAP表）

```sql
SELECT NLS_LOWER('AaA') RES FROM DUAL;

RES
-----
aaa

SELECT NLS_LOWER('AaA','NLS_SORT=CHINESE_PINYIN') RES FROM DUAL;

RES
-----
aaa
```
