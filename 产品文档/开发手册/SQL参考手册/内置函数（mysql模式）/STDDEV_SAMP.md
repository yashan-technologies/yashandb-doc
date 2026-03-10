```ebnf+diagram
stddev_samp::= STDDEV_SAMP "(" [ALL] expr ")"
```

STDDEV_SAMP函数计算[expr](../通用SQL语法/expr)的值的样本标准差。

返回类型为double类型。

其中expr的值为数值型或可以转换为NUMBER类型的字符型。对于其他类型，函数返回类型不支持。

在单行计算中，STDDEV_SAMP函数的计算结果为0。

在多行计算中，系统忽略数值为空的行，当所有行均为空时，计算结果为NULL。

聚集函数不可嵌套，因此expr为除聚集函数之外的其他通用表达式。

**ALL**

默认值，表示不对表达式输入的重复数据进行过滤，直接进行样本标准差计算。

示例（单机HEAP表）

示例

```sql
-- area1表中包含如下字段和数据
SELECT area_no,area_name,DHQ,employee_count FROM area1;
AREA_NO AREA_NAME           DHQ             EMPLOYEE_COUNT 
------- ------------------- --------------- -------------- 
01      华东              Shanghai                         
02      华西              Chengdu                    300   
03      华南              Guangzhou                  400   
04      华北              Beijing                    300   
05      华中              Wuhan                       
 
--计算员工数量的样本标准差,为空的行将被忽略,该语句等同于SELECT STDDEV(ALL employee_count) FROM area
SELECT STDDEV_SAMP(employee_count) res FROM area1;
                 RES
--------------------
          57.7350269    
 
--只有一行记录时，样本标准差为0
SELECT STDDEV_SAMP(employee_count) res FROM area1 WHERE area_no='02';
                 RES
--------------------
                   0
```

