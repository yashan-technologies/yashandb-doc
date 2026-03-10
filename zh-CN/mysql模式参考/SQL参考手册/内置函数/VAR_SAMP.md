```ebnf+diagram
var_samp::= VAR_SAMP "(" [ALL] expr ")"
```

VAR_SAMP函数计算[expr](../通用SQL语法/expr)的值的样本方差。

VAR_SAMP函数的返回值类型有以下几种情况：

返回类型为double类型。

在单行计算中，VAR_SAMP函数的计算结果为0。

在多行计算中，系统忽略数值为空的行，当所有行均为空时，计算结果为NULL。

聚集函数不可嵌套，因此expr为除聚集函数之外的其他通用表达式。

**ALL**

默认值，表示不对表达式输入的重复数据进行过滤，直接进行样本标准差计算。

示例（单机HEAP表）

```sql
--计算员工数量的样本方差,为空的行将被忽略
SELECT VAR_SAMP(employee_count) res FROM area1;
                 res
--------------------
          3333.33333
 
--只有一行记录的样本方差为NULL
SELECT VAR_SAMP(employee_count) res FROM area1 WHERE area_no='02';
                 res
--------------------
                    
```
