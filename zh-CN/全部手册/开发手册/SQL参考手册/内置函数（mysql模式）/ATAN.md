```ebnf+diagram
atan::= ATAN  "(" expr ")" 
```

ATAN函数返回给定参数的反正切值，参数为以弧度表示的角度，大小本身无限制（只受限于其所属数据类型所规定范围），函数返回一个大小在区间[-pi/2,pi/2]的DOUBLE类型数据。

其中[expr](../通用SQL语法/expr)的值为数值型或可以转换为DOUBLE类型的字符型。对于其他类型，函数返回类型不支持。

当expr的值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT ATAN(-1) res FROM DUAL;
        RES
-----------
 -7.85E-001

SELECT ATAN(0) res FROM DUAL;
        RES
-----------
          0

SELECT ATAN(1) res FROM DUAL;
        RES
-----------
 7.854E-001
```
