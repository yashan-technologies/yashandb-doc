```ebnf+diagram
cot::= COT  "(" expr ")" 
```

COT函数返回给定参数的余切值，参数为以弧度表示的角度，大小本身无限制（只受限于其所属数据类型所规定范围），函数将返回一个DOUBLE类型数据。

其中[expr](../通用SQL语法/expr)的值为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

当expr的值为NULL时，函数返回NULL。



示例

```sql
--计算以弧度表示的expr计算结果的余切值
SELECT COT(0) res FROM DUAL;
        RES
-----------
        Inf

SELECT COT(45*3.1415926/180) res FROM DUAL;
        RES 
----------- 
   1.0E+000

SELECT COT(60*3.1415926/180) res FROM DUAL;
        RES 
----------- 
 5.774E-001
```

