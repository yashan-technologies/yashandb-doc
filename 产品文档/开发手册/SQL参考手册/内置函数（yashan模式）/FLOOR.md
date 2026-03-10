```ebnf+diagram
floor::= FLOOR "(" expr ")"
```

FLOOR函数对给定参数[expr](../通用SQL语法/expr)的值进行向下取整，其返回类型为：

*   当expr的值为数值型数据时，返回与其相同类型的数据。
*   当expr的值为字符型数据时，返回NUMBER类型的数据。
*   当expr的值为NULL时，返回NULL。

其中expr的值为数值型或可以转换为NUMBER类型的字符型（转换失败返回Invalid number错误）。对于其他类型，函数返回类型不支持。



示例

```sql
SELECT FLOOR(7) floor1,
FLOOR('0.97') floor2,
FLOOR(6.22) floor3
FROM DUAL;
      FLOOR1      FLOOR2      FLOOR3
------------ ----------- -----------
           7           0           6
```

