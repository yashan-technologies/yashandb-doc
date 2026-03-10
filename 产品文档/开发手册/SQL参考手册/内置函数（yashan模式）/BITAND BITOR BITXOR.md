```ebnf+diagram
bitand::= BITAND "(" expr1 "," expr2 ")"

bitor::= BITOR "(" expr1 "," expr2 ")"

bitxor::= BITXOR "(" expr1 "," expr2 ")"
```

BITAND/BITOR/BITXOR函数将两个数据进行按位与/按位或/按位异或计算，得到按位的0或1，并将多位0或1由二进制转换为十进制数值返回。

[expr](../通用SQL语法/expr)1、[expr](../通用SQL语法/expr)2的值为TINYINT、SMALLINT、INT、BIGINT、NUMBER类型数据，或可以转换为NUMBER类型的字符型数据（转换失败返回类型转换错误）。对于其他类型，返回类型不支持错误。

函数将先对expr1、expr2执行二进制转换，然后进行位运算，转换规则及约束同[BIN](./BIN)函数。

当expr1或expr2中任一值为NULL时，函数返回NULL。

  

示例

```sql
SELECT BITAND('',1) b1,
BITAND('2.35',3) b2,
BITOR(5,-5) b3,
BITXOR(3456,1.99) b4
FROM DUAL;
                   B1                    B2                    B3                    B4
--------------------- --------------------- --------------------- ---------------------
                                          2                    -1                  3457
```

