```ebnf+diagram
atan2::= ATAN2  "(" expr1 "," expr2 ")" 
```

ATAN2函数返回给定参数 expr1/expr2 的结果的反正切值，参数以弧度表示，大小本身无限制（只受限于其所属数据类型所规定范围），函数将返回一个大小在区间[-pi,pi]的DOUBLE类型数据。

其中[expr](../通用SQL语法/expr)1和[expr](../通用SQL语法/expr)2的值均为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。

当expr1或expr2中任一值为NULL时，函数返回NULL。

示例

```sql
SELECT ATAN2(1,1) res FROM DUAL;
        RES
-----------
 7.854E-001

SELECT ATAN2(1,2) res FROM DUAL;
        RES
-----------
 4.636E-001

SELECT ATAN2(2,2) res FROM DUAL;
        RES
-----------
 7.854E-001
```
