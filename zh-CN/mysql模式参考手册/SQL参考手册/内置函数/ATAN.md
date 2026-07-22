```ebnf
atan = ATAN  "(" expr ")" .
```

ATAN函数返回给定参数的反正切值，参数为以弧度表示的角度，大小本身无限制（只受限于其所属数据类型所规定范围），函数返回一个大小在区间[-pi/2,pi/2]的DOUBLE类型数据。

其中[expr](../通用SQL语法/expr)的值为数值型或可以转换为DOUBLE类型的字符型。对于其他类型，函数返回类型不支持。

当expr的值为NULL时，函数返回NULL。

不允许对BLOB和TEXT类型数据使用此函数。

示例（HEAP表）

```sql
SELECT ATAN(-1) res FROM DUAL;
        res
-----------
 -7.85E-001

SELECT ATAN(0) res FROM DUAL;
        res
-----------
          0

SELECT ATAN(1) res FROM DUAL;
        res
-----------
 7.854E-001
```
