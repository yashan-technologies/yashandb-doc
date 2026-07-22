```ebnf
sqrt = SQRT "(" expr ")".
```

SQRT函数计算[expr](../通用SQL语法/expr)表示的数据的平方根，返回一个DOUBLE类型的数值。

其中expr的值为数值型或可以转换为DOUBLE类型的字符型。对于其他类型，函数返回类型不支持。

当expr的值为负数时，函数返回NULL。

示例（HEAP表）

```sql
SELECT SQRT(4) res;
                  res 
--------------------- 
             2.0E+000
```
