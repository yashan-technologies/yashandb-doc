```ebnf+diagram
acos::= ACOS  "(" expr ")" 
```

ACOS函数计算给定参数的反余弦值，参数为弧度表示，大小在区间[-1,1]，函数将返回一个大小在区间[0,pi]的DOUBLE类型数据。

其中[expr](../通用SQL语法/expr)的值为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

当expr的值为NULL时，函数返回NULL。



示例

```sql
SELECT ACOS(-1) res FROM DUAL;
        RES
-----------
 3.142E+000

SELECT ACOS(0) res FROM DUAL;
        RES
-----------
 1.571E+000

SELECT ACOS(1) res FROM DUAL;
        RES
-----------
          0
```

