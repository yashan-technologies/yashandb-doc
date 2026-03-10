```ebnf+diagram
bin::= BIN "(" expr ")"
```

BIN函数将[expr](../通用SQL语法/expr)表示的一个数据转换成BIT二进制，并以VARCHAR类型承载该BIT值进行返回。

**expr**

通用表达式，其值须为RAW、LOB（不得超过65534字节）、数值型或可转换为NUMBER类型的其他类型，取值范围为\[-2<sup>63</sup>, 2<sup>63</sup> - 1\]。

- 当expr的值为带有小数的NUMBER类型（或转换后为NUMBER类型）时，函数将直接截取整数位。

- 当expr的值为负数时，函数返回对应数值的64位补码。

- 当expr为NULL时，函数返回NULL。

示例（HEAP表）

```sql
SELECT BIN('2345.34') res FROM DUAL;
res
--------------
100100101001 
 
SELECT BIN(-235) res FROM DUAL;
res
----------------------------------------------------------------
1111111111111111111111111111111111111111111111111111111100010101

SELECT BIN(b'0100101010') res FROM DUAL;
[1:8]YAS-04401 data type CHAR OR STRING expected, but BIT got
 
```
