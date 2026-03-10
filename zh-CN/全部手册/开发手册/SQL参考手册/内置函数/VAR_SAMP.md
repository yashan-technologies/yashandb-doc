```ebnf+diagram
var_samp::= VAR_SAMP "(" expr ")"
```

VAR_SAMP函数计算[expr](../通用SQL语法/expr)的值的样本方差。

当给定参数只有一行数据时，VARIANCE函数的计算结果为NULL。 

VAR_SAMP函数的返回值类型有以下几种情况：

*   当expr的值为TINYINT、SMALLINT、INT、BIGINT、NUMBER类型时，返回NUMBER类型。
*   当expr的值为FLOAT、DOUBLE类型时，返回与expr的值一致的FLOAT、DOUBLE类型。
*   当expr的值为NULL时，返回NULL。
*   当expr的值为字面量、变量、常量时，返回0。
*   当expr的值为其他类型且无法转换为NUMBER类型时，不执行计算并返回类型转换错误。

在多行计算中，系统忽略数值为空的行，当所有行均为空时，计算结果为NULL。

示例

```sql
--计算员工数量的样本方差,为空的行将被忽略
SELECT VAR_SAMP(employee_count) res FROM area1;
                 RES
--------------------
          3333.33333
 
--只有一行记录的样本方差为NULL
SELECT VAR_SAMP(employee_count) res FROM area1 WHERE area_no='02';
                 RES
--------------------
                    
```
