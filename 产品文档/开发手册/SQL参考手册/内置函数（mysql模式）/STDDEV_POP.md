```ebnf+diagram
stddev_pop::= STDDEV_POP "(" [ALL] expr ")"
```

STDDEV_POP函数计算[expr](../通用SQL语法/expr)的值的总体标准差。

返回类型为double类型。

其中expr的值为数值型或可以转换为NUMBER类型的字符型。对于其他类型，函数返回类型不支持。

在单行计算中，STDDEV_POP函数的计算结果为0。

在多行计算中，系统忽略数值为空的行，当所有行均为空时，计算结果为NULL。

聚集函数不可嵌套，因此expr为除聚集函数之外的其他通用表达式。

**ALL**

默认值，表示不对表达式输入的重复数据进行过滤，直接进行总体标准差计算。

示例（单机HEAP表）

```sql
-- area1表中包含如下字段和数据
SELECT area_no,area_name,DHQ,employee_count FROM area1;
AREA_NO AREA_NAME          DHQ            EMPLOYEE_COUNT
------- ------------------ -------------- --------------
01      华东             Shanghai                       
02      华西             Chengdu                   300  
03      华南             Guangzhou                 400  
04      华北             Beijing                   300  
05      华中             Wuhan           
 
-- 计算员工数量的总体标准差,为空的行将被忽略
SELECT STDDEV_POP(employee_count) res FROM area1;
RES
--------------------
47.1404521
```

