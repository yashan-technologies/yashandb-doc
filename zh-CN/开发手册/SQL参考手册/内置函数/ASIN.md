```ebnf
asin = ASIN  "(" expr ")" .
```

ASIN函数返回给定参数的反正弦值，参数以弧度表示，大小在区间[-1,1]，函数将返回一个大小在区间[-pi/2,pi/2]的DOUBLE类型数据。

其中[expr](../通用SQL语法/expr)的值为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

当expr的值为NULL时，函数返回NULL。

示例

```sql
SELECT ASIN(0) res FROM DUAL;
        RES
-----------
          0

SELECT ASIN(-1) res FROM DUAL;
        RES
-----------
 -1.57E+000

SELECT ASIN(1) res FROM DUAL;
        RES
-----------
 1.571E+000

SELECT ASIN(0.5) res FROM DUAL;
        RES
-----------
 5.236E-001
```
