```ebnf+diagram
stddev::= STDDEV "(" [DISTINCT|ALL] expr ")"
```

STDDEV函数计算[expr](../通用SQL语法/expr)的值的总体标准差，为[STDDEV_POP](./STDDEV_POP)的同义词。

返回类型为double类型。

其中expr的值为数值型或可以转换为NUMBER类型的字符型。对于其他类型，函数返回类型不支持。

在单行计算中，STDDEV函数的计算结果为0。

在多行计算中，系统忽略数值为空的行，当所有行均为空时，计算结果为NULL。

聚集函数不可嵌套，因此expr为除聚集函数之外的其他通用表达式。

**DISTINCT**

表示过滤掉输入的重复数据后，进行样本标准差计算。

**ALL**

默认值，表示不对表达式输入的重复数据进行过滤，直接进行总体标准差计算。

示例（单机HEAP表）

```sql
-- area1表中包含如下字段和数据
SELECT area_no,area_name,DHQ,employee_count FROM area1;
area_no area_name          DHQ            employee_count
------- ------------------ -------------- --------------
01      EastChina             Shanghai                       
02      WestChina             Chengdu                   300  
03      SouthChina             Guangzhou                 400  
04      NorthChina             Beijing                   300  
05      CentralChina             Wuhan           
 
-- 计算员工数量的总体标准差,为空的行将被忽略
SELECT STDDEV(employee_count) res FROM area1;
res
--------------------
47.1404521
```
