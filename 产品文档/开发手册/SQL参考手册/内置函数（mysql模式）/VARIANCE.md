```ebnf+diagram
variance::= VARIANCE "(" [ALL] expr ")"
```

VARIANCE函数计算[expr](../通用SQL语法/expr)的值的总体方差，是[VAR_POP](./VAR_POP)的同义词。

返回类型为double类型。

在单行计算中，VARIANCE函数的计算结果为0。

在多行计算中，系统忽略数值为空的行，当所有行均为空时，计算结果为NULL。

聚集函数不可嵌套，因此expr为除聚集函数之外的其他通用表达式。

**ALL**

默认值，表示不对表达式输入的重复数据进行过滤，直接进行总体方差计算。

示例（单机HEAP表）

```sql
--计算员工数量的总体方差,为空的行将被忽略
SELECT VARIANCE(employee_count) res FROM area1;
 
                 RES
--------------------
          2222.22222
```


